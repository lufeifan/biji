### 一些常用的Validation注解

<table>
<thead>
<tr>
<th align="left">注解</th>
<th align="left">作用</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">@NotNull</td>
<td align="left">值不能为空</td>
</tr>
<tr>
<td align="left">@Null</td>
<td align="left">值必须为空</td>
</tr>
<tr>
<td align="left">@Pattern(regex=)</td>
<td align="left">字符串必须匹配正则表达式</td>
</tr>
<tr>
<td align="left">@Size(min, max)</td>
<td align="left">集合或者数组元素的数量必须在min和max之间</td>
</tr>
<tr>
<td align="left">@CreditCardNumber(ignoreNonDigitCharacters=)</td>
<td align="left">字符串必须是信用卡号，按找美国的标准验证</td>
</tr>
<tr>
<td align="left">@Email</td>
<td align="left">字符串必须是Email地址</td>
</tr>
<tr>
<td align="left">@Length(min, max)</td>
<td align="left">检查字符串的长度</td>
</tr>
<tr>
<td align="left">@NotBlank</td>
<td align="left">字符串不能为空串</td>
</tr>
<tr>
<td align="left">@NotEmpty</td>
<td align="left">字符串不能为null, 集合或者数组的 size 不能为空</td>
</tr>
<tr>
<td align="left">@Range(min, max)</td>
<td align="left">数字必须大于min, 小于max</td>
</tr>
<tr>
<td align="left">@SafeHtml</td>
<td align="left">字符串必须是安全的html</td>
</tr>
<tr>
<td align="left">@URL</td>
<td align="left">字符串必须是合法的URL</td>
</tr>
<tr>
<td align="left">@AssertFalse</td>
<td align="left">值必须是false</td>
</tr>
<tr>
<td align="left">@AssertTrue</td>
<td align="left">值必须是true</td>
</tr>
<tr>
<td align="left">@DecimalMax(value=, inclusive=)</td>
<td align="left">值必须小于等于(inclusive=true)/小于(inclusive=false)属性指定的值，也可以注释在字符串类型的属性上。</td>
</tr>
<tr>
<td align="left">@DecimalMin(value=, inclusive=)</td>
<td align="left">值必须大于等于(inclusive=true)/小于(inclusive=false)属性指定的值，也可以注释在字符串类型的属性上。</td>
</tr>
<tr>
<td align="left">@Digist(integer=,fraction=)</td>
<td align="left">数字格式检查。integer指定整数部分的最大长度，fraction指定小数部分的最大长度</td>
</tr>
<tr>
<td align="left">@Future</td>
<td align="left">时间必须是未来的</td>
</tr>
<tr>
<td align="left">@Past</td>
<td align="left">事件必须是过去的</td>
</tr>
<tr>
<td align="left">@Max(value=)</td>
<td align="left">值必须小于等于value指定的值。不能注解在字符串类型属性上。</td>
</tr>
<tr>
<td align="left">@Min(value=)</td>
<td align="left">值必须小于等于value指定的值。不能注解在字符串类型属性上。</td>
</tr>
</tbody>
</table>




```xml
        <dependency>
            <groupId>org.hibernate.validator</groupId>
            <artifactId>hibernate-validator</artifactId>
            <version>6.0.19.Final</version>
        </dependency>
        <dependency>
            <groupId>jakarta.validation</groupId>
            <artifactId>jakarta.validation-api</artifactId>
            <version>2.0.2</version>
        </dependency>
```

