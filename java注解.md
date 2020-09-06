
<h1><a name="t1"></a><a name="t1"></a><a id="_2"></a>正文</h1>
<p>&nbsp;&nbsp;&nbsp;&nbsp; 注解是一种能被添加到java源代码中的元数据，方法、类、参数和包都可以用注解来修饰。注解可以看作是一种特殊的标记，可以用在方法、类、参数和包上，程序在编译或者运行时可以检测到这些标记而进行一些特殊的处理。</p>
<h2><a name="t2"></a><a name="t2"></a><a id="tabletrtd_bgcolorgreen__font_size4_colorwhitetdtrtable_4"></a><div class="table-box"><table><tbody><tr><td bgcolor="green"> <font size="4" color="white">注解的基本元素</font></td></tr></tbody></table></div></h2>
<p>&nbsp;&nbsp;&nbsp;&nbsp; 声明一个注解要用到的东西</p>
<ul>
<li>修饰符<br>
&nbsp;&nbsp;访问修饰符必须为public,不写默认为pubic；</li>
<li>关键字<br>
&nbsp;&nbsp;关键字为@interface；</li>
<li>注解名称<br>
&nbsp;&nbsp;注解名称为自定义注解的名称，使用时还会用到；</li>
<li>注解类型元素<br>
&nbsp;&nbsp;注解类型元素是注解中内容，可以理解成自定义接口的实现部分；</li>
</ul>
<pre class="prettyprint"><code class="has-numbering" onclick="mdcp.copyCode(event)" style="position: unset;">public @interface Info {
    String value() default "tracy";
    boolean isDelete();
}
<div class="hljs-button {2}" data-title="复制"></div></code><ul class="pre-numbering" style=""><li style="color: rgb(153, 153, 153);">1</li><li style="color: rgb(153, 153, 153);">2</li><li style="color: rgb(153, 153, 153);">3</li><li style="color: rgb(153, 153, 153);">4</li></ul></pre>
<h2><a name="t3"></a><a name="t3"></a><a id="tabletrtd_bgcolorgreen__font_size4_colorwhitetdtrtable_22"></a><div class="table-box"><table><tbody><tr><td bgcolor="green"> <font size="4" color="white">使用元注解修饰注解</font></td></tr></tbody></table></div></h2>
<p>&nbsp;&nbsp;&nbsp;&nbsp; JDK中有一些元注解，主要有@Target，@Retention,@Document,@Inherited用来修饰注解。<br>
<strong>@Target</strong><br>
&nbsp;&nbsp;表明该注解可以应用的java元素类型</p>


<div class="table-box"><table>
<thead>
<tr>
<th>Target类型</th>
<th>描述</th>
</tr>
</thead>
<tbody>
<tr>
<td>ElementType.TYPE</td>
<td>应用于类、接口（包括注解类型）、枚举</td>
</tr>
<tr>
<td>ElementType.FIELD</td>
<td>应用于属性（包括枚举中的常量）</td>
</tr>
<tr>
<td>ElementType.METHOD</td>
<td>应用于方法</td>
</tr>
<tr>
<td>ElementType.PARAMETER</td>
<td>应用于方法的形参</td>
</tr>
<tr>
<td>ElementType.CONSTRUCTOR</td>
<td>应用于构造函数</td>
</tr>
<tr>
<td>ElementType.LOCAL_VARIABLE</td>
<td>应用于局部变量</td>
</tr>
<tr>
<td>ElementType.ANNOTATION_TYPE</td>
<td>应用于注解类型</td>
</tr>
<tr>
<td>ElementType.PACKAGE</td>
<td>应用于包</td>
</tr>
<tr>
<td>ElementType.TYPE_PARAMETER</td>
<td>1.8版本新增，应用于类型变量）</td>
</tr>
<tr>
<td>ElementType.TYPE_USE</td>
<td>1.8版本新增，应用于任何使用类型的语句中（例如声明语句、泛型和强制转换语句中的类型）</td>
</tr>
</tbody>
</table></div><p><strong>@Retention</strong><br>
&nbsp;&nbsp;表明该注解的生命周期</p>

<div class="table-box"><table>
<thead>
<tr>
<th>生命周期类型</th>
<th>描述</th>
</tr>
</thead>
<tbody>
<tr>
<td>RetentionPolicy.SOURCE</td>
<td>编译时被丢弃，不包含在类文件中</td>
</tr>
<tr>
<td>RetentionPolicy.CLASS</td>
<td>JVM加载时被丢弃，包含在类文件中，默认值</td>
</tr>
<tr>
<td>RetentionPolicy.RUNTIME</td>
<td>由JVM 加载，包含在类文件中，在运行时可以被获取到</td>
</tr>
</tbody>
</table></div><p><strong>@Document</strong><br>
&nbsp;&nbsp;表明该注解标记的元素可以被Javadoc 或类似的工具文档化<br>
<strong>@Inherited</strong><br>
&nbsp;&nbsp;表明使用了@Inherited注解的注解，所标记的类的子类也会拥有这个注解</p>



# Java中通过Class类获取Class对象的方法详解

<div id="cnblogs_post_body" class="blogpost-body">
    <p><strong>方式1：通过Object类的getObject()方法　</strong></p>
<div class="cnblogs_code">
<pre>Person p = <span style="color: #0000ff;">new</span><span style="color: #000000;"> Person();
Class c </span>= p.getClass();</pre>
</div>
<p><strong>方式2: 通过 类名.class 获取到字节码文件对象（任意数据类型都具备一个class静态属性,看上去要比第一种方式简单）。</strong></p>
<div class="cnblogs_code">
<pre>Class c2 = Person.<span style="color: #0000ff;">class</span>;</pre>
</div>
<p><strong>方式3: 通过Class类中的方法（将类名作为字符串传递给Class类中的静态方法forName即可）</strong></p>
<div class="cnblogs_code">
<pre>Class c3 = Class.forName("Person");</pre>
</div>
<p><strong><span style="color: #ff0000;">注意</span>：</strong>第三种和前两种的区别</p>
<p>前两种你必须明确Person类型.</p>
<p>后面是指定这种类型的字符串就行.这种<span style="color: #ff0000;">扩展更强</span>.我不需要知道类名.我只提供字符串,按照配置文件加载就可以了</p>


