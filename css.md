## 块元素







## 内联元素

一般不用内联元素包含块元素





```html
<html>

<body>
    <div>
        <!-- 缩写 -->
        The <abbr title="People's Republic of China">PRC</abbr> was founded in 1949.
    </div>
    <!-- 标签定义文档或文章的作者/拥有者的联系信息。 -->
    <address>
        Written by <a href="mailto:webmaster@example.com">Donald Duck</a>.<br>
        Visit us at:<br>
        Example.com<br>
        Box 564, Disneyland<br>
        USA
    </address>
    <!-- 粗体 -->
    <b>WWW</b>

    <!-- <div>123 <bdi dir="rtl">Steve</bdi> 456</div> -->

    <!-- 定义文字的方向 -->
    <!-- ltr 左->右 -->
    <!-- rtl 右->左 -->
    <bdo dir="ltr">Here is some text</bdo>
    <!-- 引用块 -->
    <blockquote>
        Here is a long quotation here is a long quotation
    </blockquote>
    <!-- 删除 -->
    <del>
        123
    </del>
    <!-- 斜体 -->
    <i>斜体</i>
    <!-- ins 被插入文本 -->
    <div>a dozen is <del>20</del> <ins>12</ins> pieces</div>
    <!-- mark 突出显示文本 -->
    <p>Do not forget to buy <mark>milk</mark> today.</p>

    <!-- 使用 meter 元素来度量给定范围（gauge）内的数据： -->
    <p>显示度量值：</p>
    <meter value="3" min="0" max="10">3/10</meter><br>
    <meter value="0.6">60%</meter>
    <p><b>注释：</b>Internet Explorer 不支持 meter 标签。</p>
    <!-- pre 元素可定义预格式化的文本。被包围在 pre 元素中的文本通常会保留空格和换行符。而文本也会呈现为等宽字体。 -->
    <pre>
        pre        元素可定义预格式化的文本。被包围在 pre 元素中的文本通常会保留空格和换行符。而文本也会呈现为等宽字体。
    </pre>
    <!-- <progress> 标签标示任务的进度（进程）。 -->
    <progress value="22" max="100"></progress>
    <!-- 短引用 -->
    <q>Here is a short quotation here is a short quotation</q>

    <div> 这段文本包含 <sup>上标</sup></div>
    <div>这段文本包含 <sub>下标</sub></div>
    <!-- select 元素可创建单选或多选菜单。 -->
    <select>
        <option value="volvo">Volvo</option>
        <option value="saab">Saab</option>
        <option value="opel">Opel</option>
        <option value="audi">Audi</option>
    </select>


    <select>
        <optgroup label="Swedish Cars">
            <option value="volvo">Volvo</option>
            <option value="saab">Saab</option>
        </optgroup>

        <optgroup label="German Cars">
            <option value="mercedes">Mercedes</option>
            <option value="audi">Audi</option>
        </optgroup>
    </select>
    <form>
        <!-- fieldset 定义围绕表单中元素的边框。 -->
        <!-- legend 元素为 fieldset 元素定义标题（caption） -->
        <fieldset>
            <legend>health information</legend>
            height: <input type="text" />
            weight: <input type="text" />
        </fieldset>

    </form>
    <!-- <datalist> 标签定义选项列表。请与 input 元素配合使用该元素，来定义 input 可能的值。

        datalist 及其选项不会被显示出来，它仅仅是合法的输入值列表。 -->

    <input id="myCar" list="cars" />
    <datalist id="cars">
        <option value="BMW">
        <option value="Ford">
        <option value="Volvo">
    </datalist>

    <!-- 导航栏 -->
    <nav>
        <a href="index.asp">Home</a>
        <a href="html5_meter.asp">Previous</a>
        <a href="html5_noscript.asp">Next</a>
    </nav>
</body>

</html>
```

# css格式化

```css
        body,
        div,
        dl,
        dt,
        dd,
        ul,
        ol,
        li,
        h1,
        h2,
        h3,
        h4,
        h5,
        h6,
        pre,
        form,
        fieldset,
        input,
        textarea,
        p,
        blockquote,
        th,
        td {
            margin: 0;
            padding: 0;
        }

        table {
            border-collapse: collapse;
            border-spacing: 0;
        }

        fieldset,
        img {
            border: 0;
        }

        address,
        caption,
        cite,
        code,
        dfn,
        em,
        strong,
        th,
        var {
            font-style: normal;
            font-weight: normal;
        }

        ol,
        ul {
            list-style: none;
        }

        caption,
        th {
            text-align: left;
        }

        h1,
        h2,
        h3,
        h4,
        h5,
        h6 {
            font-size: 100%;
            font-weight: normal;
        }

        q:before,
        q:after {
            content: ”;
        }

        abbr,
        acronym {
            border: 0;
        }
```

