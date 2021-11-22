# 使用Itext实现html转pdf，并解决中文乱码


> html转pdf，主要问题有：中文乱码，粗体不加粗，样式冲突等问题

​

## 场景分析
需求描述：业务系统需要导出一个专家人员信息pdf表，可供打印


主要思路有两种：

1. html模板，替换html模板内容，导出pdf

      优点：html编制更适合程序员
缺点：css样式可能部分不兼容

2. word模板，先导出word再导出pdf

优点：模板编制可由业务人员编制，word所见即所得
缺点：实现相对麻烦一，程序相对复杂。itext对中文字体支持
​

## 具体实现
​

本文主要以html模板实现为主
主要思路：

1. 读取`resource`目录下的`html`模板流（这里需要注意，业务打成jar包后，`resource`目录下的文件只能以流的方式获取。 特别是`Spring`框架自带的`ResourceUtils`类，在本地测测试可以获取到，打成jar包后就获取不到resource目录下的文件了）
1. 读取到html模板流以后转为String，可由多种方法转String
1. 转`String`以后，替换模板参数，我所用的是`hutool`的`StrUtil.format(String template, Map<String, Object> map)`方法
1. 最后将html转为pdf
```java
import cn.hutool.core.util.StrUtil;
import com.itextpdf.html2pdf.ConverterProperties;
import com.itextpdf.html2pdf.HtmlConverter;
import com.itextpdf.io.font.FontProgramFactory;
import com.itextpdf.io.util.ResourceUtil;
import com.itextpdf.layout.font.FontProvider;
import lombok.SneakyThrows;
import org.apache.commons.io.IOUtils;
import org.apache.poi.xwpf.usermodel.XWPFDocument;
import org.apache.poi.xwpf.usermodel.XWPFParagraph;
import org.springframework.util.ResourceUtils;

import java.io.*;
import java.nio.charset.StandardCharsets;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * @author wislove
 */
public class PdfUtil {

    public static void htmlTemplate2pdf(String template, Map<String, Object> paramsMap, OutputStream outputStream) {
        // 获取html内容
        String htmlStr = readTemplateStr(template, paramsMap);
        // html内容转pdf
        html2pdf(htmlStr, outputStream);
    }

    @SneakyThrows
    public static void html2pdf(String htmlStr, OutputStream outputStream) {

        // 解决中文字体不显示的bug
        ConverterProperties converterProperties = new ConverterProperties();
        // 添加编码
        converterProperties.setCharset(StandardCharsets.UTF_8.toString());
        FontProvider fontProvider = new FontProvider();
        // 这里添加的字体是windows自带的“华文仿宋”字体防止中文不显示，也可以添加其他中文字体
        fontProvider.addFont(FontProgramFactory.createFont("template/STFANGSO.TTF", false));
        // 这里添加粗体字体,防止 加粗的字体 不正常显示
        fontProvider.addFont(FontProgramFactory.createFont("template/Dengb.ttf", false));
        converterProperties.setFontProvider(fontProvider);

        // 输出流
        HtmlConverter.convertToPdf(htmlStr, outputStream, converterProperties);
    }

    @SneakyThrows
    private static String readTemplateStr(String template, Map<String, Object> paramsMap) {
        InputStream htmlInputStream = ResourceUtil.getResourceStream(template);

        StringWriter writer = new StringWriter();
        IOUtils.copy(htmlInputStream, writer, StandardCharsets.UTF_8.name());

        String htmlStr = writer.toString();

        // 替换模板
        return StrUtil.format(htmlStr, paramsMap);
    }
    

    public static void main(String[] args) {
        // demo
        File file = new File("D://test.pdf");

        try {
            // 这里的html不能由word另存为单网页文件的形式,目前未解决,只能写成下面格式的html
            htmlTemplate2pdf("template/评审专家信息表.html", new HashMap<>(), new FileOutputStream(file));
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```
html模板代码:
```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>评审专家注册信息表</title>
</head>
<body>
<style type="text/css">

    table,
    table tr th,
    table tr td {
        border: 1px solid #000000;
        margin: 0;

        /* 合并边框 */
        border-collapse: collapse;
        font-size: 12px;
    }

    .ttpp {
        font-size: 18px;
    }
</style>
<div style="width:710px;height:40px;"><p class="ttpp" align="center"><b>评审专家注册信息表</b></p></div>
<table width="710px">
    <tr>
        <td align="center" width="15%" height="40px">姓名</td>
        <td align="center" width="25%" height="40px">【姓名】</td>
        <td align="center" width="15%" height="40px">性别</td>
        <td align="center" width="25%" height="40px">【性别】</td>
        <td align="center" width="200px" rowspan="5"></td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">证件类型</td>
        <td align="center" width="25%" height="40px">【证件类型】</td>
        <td align="center" width="15%" height="40px">证件号</td>
        <td align="center" width="25%" height="40px">【证件号】</td>

    </tr>
    <tr>
        <td align="center" width="15%" height="40px">出生年月</td>
        <td align="center" width="25%" height="40px">【出生年月】</td>
        <td align="center" width="15%" height="40px">政治面貌</td>
        <td align="center" width="25%" height="40px">【政治面貌】</td>

    </tr>
    <tr>
        <td align="center" width="15%" height="40px">最高学历</td>
        <td align="center" width="25%" height="40px">【最高学历】</td>
        <td align="center" width="15%" height="40px">最高学位</td>
        <td align="center" width="25%" height="40px">【最高学位】</td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">毕业院校</td>
        <td align="center" width="25%" height="40px">【毕业院校】</td>
        <td align="center" width="15%" height="40px">毕业时间</td>
        <td align="center" width="25%" height="40px">【毕业时间】</td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">所学专业</td>
        <td align="center" width="25%" height="40px">【所学专业】</td>
        <td align="center" width="15%" height="40px">参加工作时间</td>
        <td align="center" width="25%" height="40px" colspan="2">【参加工作时间】</td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">从事相关工作时间</td>
        <td align="center" width="25%" height="40px">【从事相关工作时间】</td>
        <td align="center" width="15%" height="40px">工作所在地区</td>
        <td align="center" width="25%" height="40px" colspan="2">【工作所在地区】</td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">单位性质</td>
        <td align="center" width="25%" height="40px">【单位性质】</td>
        <td align="center" width="15%" height="40px">工作单位</td>
        <td align="center" width="25%" height="40px" colspan="2">【工作单位】</td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">职称</td>
        <td align="center" width="25%" height="40px">【职称】</td>
        <td align="center" width="15%" height="40px">职称评定时间</td>
        <td align="center" width="25%" height="40px" colspan="2">【职称评定时间】</td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">现从事专业</td>
        <td align="center" height="40px" colspan="4">【现从事专业】</td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">家庭电话</td>
        <td align="center" width="25%" height="40px">【家庭电话】</td>
        <td align="center" width="15%" height="40px">移动电话</td>
        <td align="center" width="25%" height="40px" colspan="2">【移动电话】</td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">单位电话</td>
        <td align="center" width="25%" height="40px">【单位电话】</td>
        <td align="center" width="15%" height="40px">联系Email</td>
        <td align="center" width="25%" height="40px" colspan="2">【联系Email】</td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">联系地址</td>
        <td align="center" width="25%" height="40px">【联系地址】</td>
        <td align="center" width="15%" height="40px">邮编</td>
        <td align="center" width="25%" height="40px" colspan="2">【邮编】</td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">申请专家类别</td>
        <td align="center" height="40px" colspan="4">【申请专家类别】</td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">申请评审的品目</td>
        <td align="center" height="40px" colspan="4">【申请评审的品目】</td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">主要工作经历</td>
        <td align="left" height="40px" colspan="4">【主要工作经历】</td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">主要研究成果及工作业绩</td>
        <td align="left" height="40px" colspan="4">【主要研究成果及工作业绩】</td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">参加何种协会、担任何种职务</td>
        <td align="leftr" height="40px" colspan="4">【参加何种协会、担任何种职务】</td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">担任何种技术指导、名誉顾问</td>
        <td align="left" height="40px" colspan="4">【担任何种技术指导、名誉顾问】</td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">评标实践经验</td>
        <td align="left" height="40px" colspan="4">【评标实践经验】</td>
    </tr>
    <tr>
        <td align="center" width="15%" height="40px">备注</td>
        <td align="left" height="40px" colspan="4">【备注】</td>
    </tr>
</table>
</body>

</html>
```
`resource`目录：

![image.png](https://cdn.nlark.com/yuque/0/2021/png/22631235/1637573433244-cc8a4255-e8ce-48df-a9a2-ce04f34a2877.png#clientId=u8d285f70-4f44-4&from=paste&height=124&id=u48ef25bb&margin=%5Bobject%20Object%5D&name=image.png&originHeight=247&originWidth=431&originalType=binary&ratio=1&size=11992&status=done&style=none&taskId=uf3aa7951-72f1-41b4-a1af-76d2ec26cb0&width=215.5)


主要问题：

1. 中文不显示

转html存在中文不显示的问题，是因为缺少中文字体，需要添加相对应的中文字体即可:字体在`C://Windows/Fonts`目录下，我用的是仿宋`STFANGSO.TTF`，将它复制到`resource`目录下 

2. 表头粗体 `<b>`和`<strong>`标签字段不加粗，是因为没有粗体字段。添加一个粗体字段即可: `Dengb.ttf`

![image.png](https://cdn.nlark.com/yuque/0/2021/png/22631235/1637574293148-039b8904-669b-40da-8c76-f27a21158e42.png#clientId=u6882663a-5487-4&from=paste&height=357&id=u9fdb21b8&margin=%5Bobject%20Object%5D&name=image.png&originHeight=714&originWidth=1511&originalType=binary&ratio=1&size=137533&status=done&style=none&taskId=ua694e409-e05f-4666-b722-8ca767c82e4&width=755.5)
​

## 引入依赖包
引入itext的html2pdf，找到itext的官方网站：[https://itextpdf.com/en](https://itextpdf.com/en) 找到最新的依赖包，具体操作如下：
找到需要的功能，例如pdfHTML，点击。 PS：其中第一个iText7 Core也是使用比较广的pdf转换工具，不过文档是英文的
![image.png](https://cdn.nlark.com/yuque/0/2021/png/22631235/1637570047324-1e608d5f-8141-4c24-8f57-05e7c70688b7.png#clientId=u8d285f70-4f44-4&from=paste&height=347&id=u9588280c&margin=%5Bobject%20Object%5D&name=image.png&originHeight=694&originWidth=1522&originalType=binary&ratio=1&size=104262&status=done&style=none&taskId=u80d17ad4-69a9-4ea8-bb67-244120068aa&width=761)
滑倒最下方，有个Maven库的地址，点进去选择合适的版本，依赖即可
![image.png](https://cdn.nlark.com/yuque/0/2021/png/22631235/1637570109122-71c6f977-a997-476e-9b2e-d6db9b6f4786.png#clientId=u8d285f70-4f44-4&from=paste&height=164&id=u493161d0&margin=%5Bobject%20Object%5D&name=image.png&originHeight=327&originWidth=1597&originalType=binary&ratio=1&size=42170&status=done&style=none&taskId=u8cff5718-d85a-4c8c-95f8-b764f75bdd6&width=798.5)
![image.png](https://cdn.nlark.com/yuque/0/2021/png/22631235/1637570350485-fd137c36-20a8-45ac-9f6d-2de4e95e5927.png#clientId=u8d285f70-4f44-4&from=paste&height=362&id=u50b1368b&margin=%5Bobject%20Object%5D&name=image.png&originHeight=724&originWidth=1384&originalType=binary&ratio=1&size=62363&status=done&style=none&taskId=ud6da5d69-9c40-4974-bd8a-87a92583abe&width=692)
![image.png](https://cdn.nlark.com/yuque/0/2021/png/22631235/1637570374623-5a90e0d9-d03e-4124-a383-7bb66a8e1f10.png#clientId=u8d285f70-4f44-4&from=paste&height=394&id=u9b037b17&margin=%5Bobject%20Object%5D&name=image.png&originHeight=788&originWidth=1890&originalType=binary&ratio=1&size=138857&status=done&style=none&taskId=u9a4d9a14-1000-4ace-b501-84f86e5ce82&width=945)
我使用的是gradle，所以引入如下依赖即可：
```groovy
dependencies {
	 implementation 'com.itextpdf:html2pdf:4.0.0'
}
```
## 
