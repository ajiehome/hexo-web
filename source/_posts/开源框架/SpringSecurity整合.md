---
title: SpringSecurity整合
date: 2023-06-07 15:58:40
tags:
categories: 
- 开源框架
---
---
title: SpringSecurity初体验
date: 2022-10-13 10:09:19.248
updated: 2022-10-13 14:40:09.804
url: /archives/springsecurity-chu-ti-yan
categories: 
- SpringSecurity
tags: 
- SpringSecurity
---


首先引入pom依赖
```xml
<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```
继承WebSecurityConfigurerAdapter复写如下方法，完成配置
```java
/**
 * @author huangJie
 * @version 1.0v
 * @date 2022/10/12 10:33
 */
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {


    @Resource
    private UserService userService;

    @Resource
    private SecurityExcludeUrlProperty securityExcludeUrlProperty;

    /**
     * 自定义的编码密码接口，这里直接返回的明码，demo不做加密编码处理
     *
     * @return 接口实现
     */
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new PasswordEncoder() {
            @Override
            public String encode(CharSequence charSequence) {
                return (String) charSequence;
            }

            @Override
            public boolean matches(CharSequence charSequence, String s) {
                return charSequence.equals(s);
            }
        };
    }

    /**
     * 配置HttpSecurity
     *
     * @param http HttpSecurity
     * @throws Exception 产生异常
     */
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .antMatchers(securityExcludeUrlProperty.getUri()).permitAll()
                .anyRequest().authenticated()

                .and()
                .addFilterBefore(new TokenFilter(userService), UsernamePasswordAuthenticationFilter.class)

                .exceptionHandling()
                .accessDeniedHandler(new CustomAccessDeniedHandler())
                .authenticationEntryPoint(new CustomAuthenticationEntryPoint())

                .and().sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS).disable()

                .formLogin().disable()
                .logout().disable()
                .csrf().disable();
    }

    /**
     * 用来配置自定义的鉴权操作
     *
     * @param auth 用来配置支持鉴权的构建器
     * @throws Exception 产生异常
     */
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        super.configure(auth);

        // auth.authenticationProvider(new AuthenticationProvider() {
        //     @Override
        //     public Authentication authenticate(Authentication authentication) throws AuthenticationException {
        //         return null;
        //     }
        //
        //     @Override
        //     public boolean supports(Class<?> authentication) {
        //         return true;
        //     }
        // });
    }
}
```

自定义HttpSecurity之后的调用链路
> Security filter chain:
  WebAsyncManagerIntegrationFilter
  SecurityContextPersistenceFilter
  HeaderWriterFilter
  TokenFilter
  RequestCacheAwareFilter
  SecurityContextHolderAwareRequestFilter
  AnonymousAuthenticationFilter
  ExceptionTranslationFilter
  FilterSecurityInterceptor

其中TokenFilter为自定义的Filter
```java
/**
 * @author huangJie
 * @version 1.0v
 * @date 2022/10/12 11:00
 */
@Slf4j
public class TokenFilter extends GenericFilterBean {

    private final UserService userService;

    public TokenFilter(UserService userService) {
        this.userService = userService;
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        String authorization = request.getHeader("Authorization");
        if (StringUtils.hasLength(authorization)){
            UserDetails userDetails = userService.loadUserByUsername(authorization);
            SecurityContextHolder.getContext().setAuthentication(
                    new UsernamePasswordAuthenticationToken(userDetails.getUsername(), userDetails.getPassword()));
        }
        filterChain.doFilter(servletRequest, servletResponse);
    }
}
```
对于用户信息获取需要实现`UserDetailsService`接口，这里并未对用户信息做复杂操作，只为过登录校验做了处理
```java
/**
 * @author huangJie
 * @version 1.0v
 * @date 2022/10/12 14:02
 */
public interface UserService extends UserDetailsService {

}

/**
 * @author huangJie
 * @version 1.0v
 * @date 2022/10/12 14:02
 */
@Service
public class UserServiceImpl implements UserService {
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        UserEntity user = new UserEntity();
        user.setUsername(username);
        user.setPassword(username);
        return user;
    }
}
```
其中`UserEntity`也是实现自UserDetails类
```java
/**
 * @author huangJie
 * @version 1.0v
 * @date 2022/10/12 10:44
 */
@Setter
public class UserEntity implements UserDetails, Serializable {
    private static final long serialVersionUID = 3532830780866118325L;


    private String username;
    private String password;
    private List<String> roles = new ArrayList<String>() {
        private static final long serialVersionUID = 8112282703158213813L;

        {
            add("ADMIN");
            add("USER");
        }
    };

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        List<GrantedAuthority> auth = new ArrayList<>();
        for (String role : roles) {
            auth.add(new SimpleGrantedAuthority(role));
        }
        return auth;
    }

    @Override
    public String getPassword() {
        return password;
    }

    @Override
    public String getUsername() {
        return username;
    }

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }
}
```