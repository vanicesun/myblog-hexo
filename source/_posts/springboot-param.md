---
title: Spring Boot开发之参数校验
date: 2019-12-10 15:14:39
categories: SpringBoot
tags: SpringBoot
---

### 一、PathVariable校验
在定义Restful风格的接口时，通常会采用PathVariable指定关键业务参数如下：
```
@GetMapping("/path/{group:[a-zA-Z0-9_]+}/{userid}")
@ResponseBody
public String path(@PathVariable("group") String group,@PathVariable("userid") Integer userid){
   return group + ":" + userid;
}
```
{group:[a-zA-Z0-9_]+} 这样的表达式指定了 group 必须是以大小写字母、数字或下划线组成的字符串。
试着访问一个错误的路径：
```
GET /path/testIllegal.get/10000
```
此时会得到**404**的响应，因此对于PathVariable 仅由正则表达式可达到校验的目的

### 二、方法参数校验
就像前面所描述的那样，大多数情况下，我们都会直接将HTTP请求参数映射到请求的方法参数上
```
@GetMapping("/param")
    @ResponseBody
    public String param(@RequestParam("group")@Email String group, 
                        @RequestParam("userid") Integer userid) {
       return group + ":" + userid;
    }
```
上面的代码中，@RequestParam 声明了映射，此外我们还为 group 定义了一个规则(复合Email格式)
这段代码是否能直接使用呢？答案是否定的，为了启用方法参数的校验能力，还需要完成以下步骤：
1.**声明 MethodValidationPostProcessor**
```
 @Bean
    public MethodValidationPostProcessor methodValidationPostProcessor() {
         return new MethodValidationPostProcessor();
    }
```
2.**Controller指定@Validated注解**
```
@Controller
    @RequestMapping("/validate")
    @Validated
    public class ValidateController {
```
如此之后，方法上的@Email规则才能生效。
**检验异常**
如果此时我们尝试通过非法参数进行访问时，比如提供非Email格式的 group会得到以下错误：
```
 GET /validate/param?group=simple&userid=10000
    ====>
    {
        "timestamp": 1530955093583,
        "status": 500,
        "error": "Internal Server Error",
        "exception": "javax.validation.ConstraintViolationException",
        "message": "No message available",
        "path": "/validate/param"
    }
```
而如果参数类型错误，比如提供非整数的 userid，会得到：
```
 GET /validate/param?group=simple&userid=1f
    ====>
    {
        "timestamp": 1530954430720,
        "status": 400,
        "error": "Bad Request",
        "exception": "org.springframework.web.method.annotation.MethodArgumentTypeMismatchException",
        "message": "Failed to convert value of type 'java.lang.String' to required type 'java.lang.Integer'; nested exception is java.lang.NumberFormatException: For input string: \"1f\"",
        "path": "/validate/param"
    }
```
当存在参数缺失时，由于定义的@RequestParam注解中，属性 required=true，也将会导致失败：
```
 GET /validate/param?userid=10000
    ====>
    {
        "timestamp": 1530954345877,
        "status": 400,
        "error": "Bad Request",
        "exception": "org.springframework.web.bind.MissingServletRequestParameterException",
        "message": "Required String parameter 'group' is not present",
        "path": "/validate/param"
    }
```
### 三、表单对象校验
页面的表单通常比较复杂，此时可以将请求参数封装到表单对象中，
并指定一系列对应的规则，参考JSR-303![](https://www.ibm.com/developerworks/cn/java/j-lo-jsr303/index.html)
```
public static class FormRequest {

        @NotEmpty
        @Email
        private String email;

        @Pattern(regexp = "[a-zA-Z0-9_]{6,30}")
        private String name;

        @Min(5)
        @Max(199)
        private int age;
```
上面定义的属性中：
email必须非空、符合Email格式规则；
name必须为大小写字母、数字及下划线组成，长度在6-30个；
age必须在5-199范围内
Controller方法中的定义：
```
@PostMapping("/form")
    @ResponseBody
    public FormRequest form(@Validated FormRequest form) {
        return form;
    }
```
**@Validated**指定了参数对象需要执行一系列校验。

**校验异常**
此时我们尝试构造一些违反规则的输入，会得到以下的结果：
```
 {
        "timestamp": 1530955713166,
        "status": 400,
        "error": "Bad Request",
        "exception": "org.springframework.validation.BindException",
        "errors": [
            {
                "codes": [
                    "Email.formRequest.email",
                    "Email.email",
                    "Email.java.lang.String",
                    "Email"
                ],
                "arguments": [
                    {
                        "codes": [
                            "formRequest.email",
                            "email"
                        ],
                        "arguments": null,
                        "defaultMessage": "email",
                        "code": "email"
                    },
                    [],
                    {
                        "arguments": null,
                        "codes": [
                            ".*"
                        ],
                        "defaultMessage": ".*"
                    }
                ],
                "defaultMessage": "不是一个合法的电子邮件地址",
                "objectName": "formRequest",
                "field": "email",
                "rejectedValue": "tecom",
                "bindingFailure": false,
                "code": "Email"
            },
            {
                "codes": [
                    "Pattern.formRequest.name",
                    "Pattern.name",
                    "Pattern.java.lang.String",
                    "Pattern"
                ],
                "arguments": [
                    {
                        "codes": [
                            "formRequest.name",
                            "name"
                        ],
                        "arguments": null,
                        "defaultMessage": "name",
                        "code": "name"
                    },
                    [],
                    {
                        "arguments": null,
                        "codes": [
                            "[a-zA-Z0-9_]{6,30}"
                        ],
                        "defaultMessage": "[a-zA-Z0-9_]{6,30}"
                    }
                ],
                "defaultMessage": "需要匹配正则表达式\"[a-zA-Z0-9_]{6,30}\"",
                "objectName": "formRequest",
                "field": "name",
                "rejectedValue": "fefe",
                "bindingFailure": false,
                "code": "Pattern"
            },
            {
                "codes": [
                    "Min.formRequest.age",
                    "Min.age",
                    "Min.int",
                    "Min"
                ],
                "arguments": [
                    {
                        "codes": [
                            "formRequest.age",
                            "age"
                        ],
                        "arguments": null,
                        "defaultMessage": "age",
                        "code": "age"
                    },
                    5
                ],
                "defaultMessage": "最小不能小于5",
                "objectName": "formRequest",
                "field": "age",
                "rejectedValue": 2,
                "bindingFailure": false,
                "code": "Min"
            }
        ],
        "message": "Validation failed for object='formRequest'. Error count: 3",
        "path": "/validate/form"
    }
```
如果是参数类型不匹配，会得到：
```
{
        "timestamp": 1530955359265,
        "status": 400,
        "error": "Bad Request",
        "exception": "org.springframework.validation.BindException",
        "errors": [
            {
                "codes": [
                    "typeMismatch.formRequest.age",
                    "typeMismatch.age",
                    "typeMismatch.int",
                    "typeMismatch"
                ],
                "arguments": [
                    {
                        "codes": [
                            "formRequest.age",
                            "age"
                        ],
                        "arguments": null,
                        "defaultMessage": "age",
                        "code": "age"
                    }
                ],
                "defaultMessage": "Failed to convert property value of type 'java.lang.String' 
    to required type 'int' for property 'age'; nested exception is java.lang.NumberFormatException: 
    For input string: \"\"",
                "objectName": "formRequest",
                "field": "age",
                "rejectedValue": "",
                "bindingFailure": true,
                "code": "typeMismatch"
            }
        ],
        "message": "Validation failed for object='formRequest'. Error count: 1",
        "path": "/validate/form"
    }
```
> Form表单参数上，使用@Valid注解可达到同样目的，而关于两者的区别则是：
> @Valid 基于JSR303，即 Bean Validation 1.0，由Hibernate Validator实现；
>  @Validated 基于JSR349，是Bean Validation 1.1，由Spring框架扩展实现；