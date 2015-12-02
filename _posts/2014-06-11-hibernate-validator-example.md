---
layout: post
title: "hibernate-validator使用"
description: "hibernate-validator使用"
category: "java"
tags: ["java", "validation"]
---
{% include JB/setup %}


项目代码(hibernate-validatioin-example)[git@github.com:epiphyllum/hibernate-validation-example.git]

定义工具类
{% highlight java %}
public class ValidationUtils {

    // get Validator
    private static Validator validator = Validation.buildDefaultValidatorFactory().getValidator();


    // 验证整个entity
    public static <T> ValidationResult validateEntity(T obj) {


        // 验证结果
        ValidationResult result = new ValidationResult();


        // violations
        Set<ConstraintViolation<T>> set = validator.validate(obj,Default.class);  // Default.class --> Group.class

        // 设置验证结果
        if( CollectionUtils.isNotEmpty(set) ){

            // has Error
            result.setHasErrors(true);

            // error message Map
            Map<String,String> errorMsg = new HashMap<String,String>();
            for(ConstraintViolation<T> cv : set){
                errorMsg.put(cv.getPropertyPath().toString(), cv.getMessage());
            }
            result.setErrorMsg(errorMsg);
        }
        return result;
    }

    // 验证实体的某个field
    public static <T> ValidationResult validateProperty(T obj,String propertyName){

        ValidationResult result = new ValidationResult();

        Set<ConstraintViolation<T>> set = validator.validateProperty(obj,propertyName,Default.class);   // validator的validateProperty方法

        if( CollectionUtils.isNotEmpty(set) ){
            result.setHasErrors(true);
            Map<String,String> errorMsg = new HashMap<String,String>();
            for(ConstraintViolation<T> cv : set){
                errorMsg.put(propertyName, cv.getMessage());
            }
            result.setErrorMsg(errorMsg);
        }
        return result;
    }
}

{% endhighlight %}

## 简单使用

定义实体类
{% highlight java %}

public class SimpleEntity {

    @NotBlank(message="名字不能为空或者空串")
    @Length(min=2,max=10,message="名字必须由2~10个字组成")
    private String name;

    @Past(message="时间不能晚于当前时间")
    private Date date;

    @Email(message="邮箱格式不正确")
    private String email;

    @Pattern(regexp="/^(?![0-9]+$)(?![a-zA-Z]+$)[0-9A-Za-z]{5,10}$/",message="密码必须是5~10位数字和字母的组合")
    private String password;

    @AssertTrue(message="字段必须为真")
    private boolean valid;

    //get set方法省略，自己添加
}

{% endhighlight %}

测试
{% highlight java %}
public class Test {
    // 原生 - 验证整个实体
    @Test
    public void validateSimpleEntity() {
        SimpleEntity se = new SimpleEntity();
        se.setDate(new Date());
        se.setEmail("123");
        se.setName("123");
        se.setPassword("123");
        se.setValid(false);
        ValidationResult result = ValidationUtils.validateEntity(se);
        System.out.println("--------------------------");
        System.out.println(result);
        // result.isHasErrors();
    }

    // 原生 - 验证实体属性
    @Test
    public void validateSimpleProperty() {
        SimpleEntity se = new SimpleEntity();
        ValidationResult result = ValidationUtils.validateProperty(se,"name");
        System.out.println("--------------------------");
        System.out.println(result);
        // result.isHasErrors();
    }
}
{% endhighlight %}

## 自定义validator @Password

一、定义@Password
{% highlight java %}
@Target( {ElementType.METHOD, ElementType.FIELD, ElementType.ANNOTATION_TYPE })
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = PasswordValidator.class)    // javax.validation.Constraint
@Documented
public @interface Password {

    String message() default "{密码必须是5~10位数字和字母组合}";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};
}
{% endhighlight %}

二、实现ConstraintValidator<Password, String>

{% highlight java %}
public class PasswordValidator implements ConstraintValidator<Password, String> {

    //5~10位的数字与字母组合
    private static Pattern pattern = Pattern.compile("(?![0-9]+$)(?![a-zA-Z]+$)[0-9A-Za-z]{5,10}");

    public void initialize(Password constraintAnnotation) {
        //do nothing
    }

    public boolean isValid(String value, ConstraintValidatorContext context) {
        if( value==null ){
            return false;
        }
        Matcher m = pattern.matcher(value);
        return m.matches();
    }
}
{% endhighlight %}

三、测试

{% highlight java %}
public class Test {
    // 子定义的Annotation!!
    @Test
    public void validateExtendedEntity() {
        ExtendedEntity ee = new ExtendedEntity();
        ee.setPassword("212");
        ValidationResult result = ValidationUtils.validateEntity(ee);
        System.out.println("--------------------------");
        System.out.println(result);
        // result.isHasErrors();
    }
}
{% endhighlight %}


