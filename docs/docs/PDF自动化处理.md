大家好，欢迎来到 Python 办公魔法分享！今天，我将带领大家探索一项神奇的技能——Python PDF 自动化处理。无论你是一位办公室战士、数据分析师，还是一名爱好者，相信这些技巧都能为你带来非凡的便利。
首先，让我们破解第一个魔法：PDF 解析和文本提取。你是否曾经想过从一堆 PDF 文件中提取有用的信息，例如报告、合同等？别担心！Python 可以帮助你转变为“提取大师”。我们可以使用库如 PyPDF2、pdfminer 和 textract 来解析 PDF，然后从中提取出文本，轻松获得所需信息。看起来魔法很简单！
接下来是我们的第二门魔法：PDF 合并和拆分。让我们想象一下，你有许多零散的 PDF 文件，需要将它们整合成一个完整的文件，或者你想将一个庞大的文件拆分成多个部分。Python 可以在这里发挥它的魔力！使用库如 PyPDF2 和 pdfrw，你可以轻松地将 PDF 文件合并或拆分。来吧，让我们扔掉那些烦人的 PDF 片段，找回整个魔法世界！
第三门魔法是 PDF 表单处理。你是否曾经被大量重复的 PDF 表单填写工作所困扰？Python 可以化身为你的“表单填充小助手”。借助库如 PyPDF2、pdfrw 和 FPDF，你可以自动填充 PDF 表单字段，读取填写的数据或者生成全新的 PDF 表单。从此，告别乏味的表单填写，迎接轻松愉快的办公时光！
我们的第四道魔法是 PDF 文档转换。你是否曾经被 PDF 文件的死板形式所束缚？好消息！Python 可以将你的 PDF 文件变成任何你想要的格式。使用库如 pdf2image、pdfminer 和 PyPDF2，你可以将 PDF 转换为图像、HTML 或纯文本。从此，PDF 不再是固化的黑白世界，开启多彩的自由转换！
第五项技能是 PDF 水印和签章。想象一下，你需要为 PDF 文件添加水印或数字签章来确保其安全和合法性。别害怕，Python 就是你的“安全卫士”。PyPDF2、pdfrw 和 reportlab 等库能够帮助你自动为 PDF 文件添加水印和签章，让文件无懈可击，充满信任与权威！
下一步，我们来探索第六门魔法：PDF 报表生成。你是否曾经希望使用 Python 生成个性化的 PDF 报表，包括图表、表格、文本等元素？那么你已找对了正确的引导。借助库如 matplotlib、reportlab 和 PyPDF2，你可以创建定制化的 PDF 报表，让数据和信息统统为你所用！
最后一项技能是 OCR（光学字符识别）。你是否曾经遇到需要从扫描的 PDF 文档中提取数据的问题？Python 将变身为你的“数据解码专家”。使用库如 PyPDF2、pdf2image 和 Tesseract OCR，你可以将扫描的 PDF 文档转换为可搜索和可编辑的文本，轻松获取所需的信息。现在，你可以从 PDF 的花丛中寻找隐藏的宝藏了！
大家看到了吗？Python PDF 自动化处理的世界是如此的令人兴奋和多彩。通过掌握这些技能，你将成为办公室中的真正魔法师，解放自己的工作效率，并且享受到更多真正有趣的事情！
### PDF 解析和文本提取
首先，我们要解开的秘密是 PDF 解析和文本提取。你是否曾经想过如何从 PDF 中提取有用的信息？别担心，Python 可以帮助你。我们可以使用 PyPDF2、FPDF 和 reportlab 这些神奇的库来解析 PDF 文件，然后从中提取出文本信息。
下面是关于 `PyPDF2` 、`FPDF` 和 `reportlab` 库的简介：

- `PyPDF2`库：`PyPDF2`是用于处理PDF文件的Python库。它提供了各种功能，包括合并、拆分、旋转、提取文本、提取页面和添加水印等。您可以使用`PyPDF2`库读取PDF文件的内容，提取文本和图像，还可以创建新的PDF文件或修改现有PDF文件。它是一个功能丰富且易于使用的库，适用于处理各种PDF操作需求。
- `FPDF`：`FPDF` 是一个用于创建 PDF 文件的 Python 库。它允许您使用 Python 代码生成包含文本、图像、表格、图形等元素的标准 PDF 文档。`FPDF` 简单易用，适合基本的 PDF 生成需求。您可以使用 `FPDF` 来创建报告、文档、证书、发票等。
- `reportlab`：`reportlab` 是一个功能强大且灵活的 Python 库，用于创建复杂的 PDF 文档。它提供了丰富的功能来处理文本、图像、表格、图形、字体等，并支持高级布局和样式。`reportlab` 是一个广泛使用的库，适用于生成专业的 PDF 报告、图书、数据可视化等。

先安装模块：
pip install PyPDF2 FPDF reportlab
安装完成后就可以愉快的玩转 PDF 文件了。
特别注意：
在学习该模块 API 时，有个需要注意的问题，就是 PdfFileReader、PdfFileWriter、PdfFileMerger 这几个类，会在3.0.0版本被移除，建议使用 PdfReader、PdfWriter、PdfMerger。
笔者安装的是 PyPDF2（3.0.1 版本），以下的代码才能正常执行，读者需要关注版本兼容问题。
```
import PyPDF2

# 打开 PDF 文件
with open('example.pdf', 'rb') as file:
  # 创建一个 PDF 阅读器对象
  reader = PyPDF2.PdfReader(file)
  
  # 获取 PDF 文件中的页数
  num_pages = len(reader.pages)
  
  # 逐页提取文本内容并打印
  for page_num in range(num_pages):
    page = reader.pages[page_num]
    text = page.extract_text()
    print(text)
```
嘿嘿，现在你可以像解码神探一样从 PDF 中抽取文字啦！快去实践试试吧。
### PDF 合并和拆分
接下来是我们的第二招：PDF 合并和拆分。想象一下你手上有很多 PDF 文件，你可以使用 Python 轻松地将它们合并成一个整洁的 PDF，或者把一个大文件拆成多个小文件。让我们试试这个搞笑的代码：
```
from PyPDF2 import PdfMerger, PdfReader, PdfWriter

# 创建一个 PDF 合并器
merger = PdfMerger()

# 合并多个 PDF 文件
merger.append('example.pdf')
merger.append('file2.pdf')

# 保存合并后的文件
merger.write('merged.pdf')
merger.close()

# 拆分一个 PDF 文件
with open('merged.pdf', 'rb') as file:
    reader = PdfReader(file)
    num_pages = len(reader.pages)
  
    # 每 10 页拆分为一个文件
    for start in range(0, num_pages, 10):
        end = min(start + 9, num_pages - 1)
        writer = PdfWriter()
    
        # 将指定范围页添加到新文件中
        for page_num in range(start, end + 1):
            writer.add_page(reader.pages[page_num])
    
        # 保存拆分的文件
        with open(f'part_{start+1}-{end+1}.pdf', 'wb') as output_file:
            writer.write(output_file)

print("Ta-da! 合并和拆分的魔法完成了！")
```
好了，现在你掌握了合并和拆分的 PDF 魔法，把你的文件整理得井井有条吧！
### PDF 表单处理
第三项技能是 PDF 表单处理。我们都知道填写大量的 PDF 表单是一件非常乏味的工作，但是别担心，Python 的魔法帮手来啦！我们可以使用 PyPDF2、pdfrw 和 FPDF 这些有趣的库来自动填充表单字段、读取已填写的数据或生成全新的 PDF 表单，让我们试试吧！
```
from PyPDF2 import PdfReader, PdfWriter

from reportlab.pdfgen import canvas

# 自动填充表单字段
def fill_form(input_file, output_file, data):
    c = canvas.Canvas(output_file)
    c.setFont("Helvetica", 12)
    
    # 读取输入文件，逐页处理
    reader = PdfReader(input_file)
    for page_num, page in enumerate(reader.pages, start=1):
        # 获取页面大小，并创建对应大小的画布
        page_width = float(page.mediabox.width)
        page_height = float(page.mediabox.width)
        c.setPageSize((page_width, page_height))
        
        # 绘制页面内容
        c.showPage()
        
        # 检查页面是否有表单字段
        if '/Annots' in page:
            # 遍历所有表单字段
            for annot in page['/Annots']:
                # 检查字段类型为文本域
                if '/T' in annot and '/V' in annot and annot['/Type'] == '/Annot':
                    field_name = annot['/T'][1:-1]  # 获取字段名称
                    # 替换字段值为传入的数据
                    if field_name in data:
                        field_value = data[field_name]
                        c.drawString(annot['/Rect'][0], annot['/Rect'][1], field_value)
    
    # 保存填充数据后的 PDF
    c.save()

# 读取已填写的数据
def read_form_data(input_file):
    data = {}
    reader = PdfReader(input_file)
    
    # 遍历所有页面
    for page in reader.pages:
        # 检查是否有表单字段
        if '/Annots' in page:
            # 遍历所有表单字段
            for annot in page['/Annots']:
                # 检查字段类型为文本域
                if '/T' in annot and '/V' in annot and annot['/Type'] == '/Annot':
                    field_name = annot['/T'][1:-1]  # 获取字段名称
                    field_value = annot['/V'][1:-1] if isinstance(annot['/V'], str) else ''
                    data[field_name] = field_value
    
    return data

# 创建新的 PDF 表单
def create_form(output_file, data):
    c = canvas.Canvas(output_file)
    c.setFont("Helvetica", 12)
    
    # 将数据逐行添加到表单中
    y = 800
    for field, value in data.items():
        c.drawString(50, y, f"{field}: {value}")
        y -= 20
    
    # 保存表单
    c.save()

# 填充表单字段并保存
fill_form('form_template.pdf', 'filled_form.pdf', {'name': '小明', 'age': '18'})

# 读取已填写的数据并打印
form_data = read_form_data('filled_form.pdf')
print(form_data)

# 创建新的 PDF 表单
create_form('my_form.pdf', {'name': '小明', 'age': '18'})

print("魔法完成！现在你可以轻松处理 PDF 表单了！")
```
嘿，现在你是表单处理的大魔法师了！让 Python 来处理这些枯燥的表单工作吧！
### PDF 文档转换
下一个魔法是 PDF 文档转换。有时候 PDF 的格式不太方便，你可能希望将 PDF 转换为其他格式，例如图像、HTML 或纯文本。让我们一起探索转换的魔法吧！
当将PDF文档转换为其他格式时，您可以使用不同的库和工具来实现。下面是针对每种转换的理论解释以及相应的示例代码：
#### 将PDF转为图像
要将PDF转换为图像，您可以使用`pdf2image`库。这个库可以将PDF页面转换为图像格式（如JPEG、PNG等）。以下是一个示例代码，将PDF转换为图像：
```
from pdf2image import convert_from_path

def pdf_to_image(input_file, output_file):
    images = convert_from_path(input_file)
    for i, image in enumerate(images):
        image.save(f'{output_file}_{i}.jpg', 'JPEG')

pdf_to_image('input.pdf', 'output_image')
```
您可以调用`pdf_to_image`函数，将输入的PDF文件转换为图像，并将结果保存为JPEG格式的图像文件。
**注意**：如果出现了`pdf2image.exceptions.PDFInfoNotInstalledError`错误提示。这个错误通常发生在缺少`poppler-utils`依赖的情况下。要解决此问题，请根据操作系统执行以下步骤：
**Windows：**

1. 访问以下网址：[https://github.com/oschwartz10612/poppler-windows/releases/](https://link.segmentfault.com/?enc=P%2FGd9FJNpU5HzU2BQtTZ%2BQ%3D%3D.raZLOKHPfSyz9QQ6%2BW1w210NUBZcJ3Z8b%2BUifhPzzuHn9qziunjEI%2Bb1L%2F4ommUA9qqN6DmSQcqKmzCSMt60kQ%3D%3D)
2. 在“Assets”部分下载适用于您的操作系统的`poppler-x.x.x_x`版本。
3. 解压下载的文件，并将其路径添加到系统环境变量中。

**macOS：**
通过Homebrew安装Poppler。运行以下命令：

1. brew install poppler

**Ubuntu/Debian：**
安装Poppler。运行以下命令：

1. sudo apt-get install poppler-utils

安装完成后，请重启您的Python环境，并再次尝试运行`pdf_to_image`函数。这样，您就可以成功将PDF转换为图像了。
#### 将PDF转为HTML
如果您希望将PDF文件转换为HTML格式，可以使用支持PDF解析的库，如`PyPDF2`。以下是一个示例代码，将PDF转换为HTML：
```
from PyPDF2 import PdfReader

def pdf_to_html(input_file, output_file):
    with open(input_file, 'rb') as file:
        reader = PdfReader(file)
        text = ""
        
        # 逐页提取文本内容
        for page in reader.pages:
            text += page.extract_text()
        
        # 保存为HTML文件
        with open(output_file, 'w') as html_file:
            html_file.write(f"<html><body>{text}</body></html>")

pdf_to_html('input.pdf', 'output.html')
```
您可以调用`pdf_to_html`函数，将输入的PDF文件转换为HTML格式，并将结果保存到输出文件中。
#### 将PDF转为纯文本
要将PDF转换为纯文本格式，您可以使用`pdfminer`库。它是一个用于提取PDF文档中文本的强大工具。以下是一个示例代码，将PDF转换为纯文本：
pdfminer.six库：这是对pdfminer库的新版本，专为Python 3编写。它是PDF解析的现代化实现，并获得了持续的维护和更新。pdfminer.six库兼容Python 2和Python 3，因此它可以在较新的Python版本中使用，同时也能支持一些旧版Python。
安装依赖：使用 pdfminer.six 库
pip install pdfminer.six
示例代码：
```
from pdfminer.high_level import extract_text_to_fp

def pdf_to_text(input_file, output_file):
    with open(output_file, 'w') as text_file:
        with open(input_file, 'rb') as file:
            extract_text_to_fp(file, text_file)

pdf_to_text('input.pdf', 'output.txt')
```
您可以调用`pdf_to_text`函数，将输入的PDF文件转换为纯文本格式，并将结果保存到输出文件中。
#### 将PDF转为Word文档
要将PDF转换为Word文档，您可以使用第三方库`python-docx`，该库可以用于创建和编辑Word文档。
下面是关于`python-docx`库的简介：

- `python-docx`库：`python-docx`是一个用于创建和修改Microsoft Word文档的Python库。它提供了一个简单而强大的API，可以让您通过代码创建、修改和操作Word文档。您可以使用`python-docx`库添加段落、字体样式、表格、图像等内容，还可以修改现有文档的样式和内容。它支持Word 2007和更高版本的docx文件格式。

安装依赖：
pip install python-docx PyPDF2
以下是一个示例代码，将PDF转换为Word文档：
```
from docx import Document
from PyPDF2 import PdfReader

def pdf_to_word(input_file, output_file):
    with open(input_file, 'rb') as file:
        reader = PdfReader(file)
        text = ""
        
        # 逐页提取文本内容
        for page in reader.pages:
            text += page.extract_text()
        
        # 创建Word文档
        doc = Document()
        doc.add_paragraph(text)
        
        # 保存为Word文档
        doc.save(output_file)

pdf_to_word('input.pdf', 'output.docx')
```
您可以调用`pdf_to_word`函数，将输入的PDF文件转换为Word文档，并将结果保存到输出文件中。
请确保在运行上述示例代码之前，已安装了相应的库和依赖项。您可以使用`pip`命令来安装缺少的库。
希望这些示例代码可以帮助您将PDF文件转换为其他格式。如有任何疑问，请随时提问！
### PDF 水印和签章
接下来是 PDF 水印和签章的魔法！想想看，给 PDF 文件加上水印或数字签章可以增加版权和安全保障。让我们一起进入这个魔法的奇幻世界：
```
from PyPDF2 import PdfReader, PdfWriter
from reportlab.pdfgen import canvas
import io

# 添加水印到 PDF
def add_watermark(input_file, output_file, watermark_text):
    reader = PdfReader(input_file)
    writer = PdfWriter()

    watermark_buffer = io.BytesIO()

    # 创建带有水印的 PDF
    c = canvas.Canvas(watermark_buffer)
    c.setFont("Helvetica", 48) # 注意字体的选择
    c.rotate(45)
    c.translate(-500, -500)
    c.setFillAlpha(0.3)
    c.drawString(400, 400, watermark_text)
    c.save()

    watermark_buffer.seek(0)
    watermark_pdf = PdfReader(watermark_buffer)

    # 遍历每个页面
    for i, page in enumerate(reader.pages, start=1):
        watermark_page = watermark_pdf.pages[0]

        # 添加水印到页面
        page.merge_page(watermark_page)
        writer.add_page(page)

    # 保存带水印的文件
    with open(output_file, 'wb') as file:
        writer.write(file)

# 添加数字签章到 PDF
def add_signature(input_file, output_file, signature_image):
    reader = PdfReader(input_file)
    writer = PdfWriter()

    # 遍历每个页面
    for i, page in enumerate(reader.pages, start=1):
        # 在页面右下角添加签章图像
        page.merge_page(signature_image)
        writer.add_page(page)

    # 保存带签章的文件
    with open(output_file, 'wb') as file:
        writer.write(file)

# 使用 add_watermark() 和 add_signature() 函数添加水印和签章
watermark_text = "机密文件，请勿泄露"
signature_image = PdfReader("signature.pdf").pages[0]

add_watermark('part_21-30.pdf', 'document_with_watermark.pdf', watermark_text)
add_signature('document_with_watermark.pdf', 'document_with_watermark_and_signature.pdf', signature_image)

print("水印和签章魔法完成！你现在可以让 PDF 文件更安全更专业了！")
```
嘿嘿嘿，现在你是 PDF 水印和签章的超级魔法师了！让文件充满魔力和保护吧！
### PDF 报表生成
现在，我们准备好挖掘最后一项魔法了！PDF 报表生成，想象一下以 Python 的力量生成各种漂亮的 PDF 报表，包括图表、表格和文本。让我们一起开启这段魔法之旅吧！
依赖安装：
pip install pytesseract
```
import matplotlib.pyplot as plt
from reportlab.lib.pagesizes import A4
from reportlab.platypus import SimpleDocTemplate, Table, Image
from reportlab.lib.styles import getSampleStyleSheet
from reportlab.platypus import Paragraph, Spacer

# 创建报表内容
def create_report(output_file, data):
    # 创建 PDF 文档对象
    doc = SimpleDocTemplate(output_file, pagesize=A4)

    # 加载样式表
    styles = getSampleStyleSheet()

    # 创建报表内容元素
    elements = []

    # 添加标题
    title = Paragraph("销售报表", styles["Title"])
    elements.append(title)
    elements.append(Spacer(1, 20))

    # 添加表格
    table_data = data
    table = Table(table_data)
    elements.append(table)
    elements.append(Spacer(1, 20))

    # 生成图表并保存为 PNG 图片
    plt.plot(data[1][1:], marker='o')
    plt.xlabel("日期")
    plt.ylabel("销售额")
    plt.title("销售趋势图")
    plt.savefig("sales_plot.png")
    plt.close()

    # 添加图表到报表内容
    image = Image("sales_plot.png", width=400, height=300)
    elements.append(image)

    # 生成报表
    doc.build(elements)

# 创建报表数据
report_data = [
    ["日期", "销售额"],
    ["1/1", 100],
    ["1/2", 200],
    ["1/3", 150],
    ["1/4", 300],
]

# 生成报表
create_report('sales_report.pdf', report_data)

print("报表生成完成！现在您可以查看生成的报表文件。")
```
哇喔！现在你成为了真正的报表魔法师，以 Python 的力量创造出惊艳的报表！
### OCR（光学字符识别）
最后一项魔法是 OCR（光学字符识别）。想象一下，你有一些扫描的 PDF 文档，需要将其中的文字转换为可搜索和可编辑的文本。好消息是，Python 可以帮你实现！我们一起来体验这项神奇的魔法！
```
import pdf2image
import pytesseract

# 将 PDF 转为图像
def pdf_to_image(input_file):
    images = pdf2image.convert_from_path(input_file)
    return images

# 使用 OCR 将图像转为文本
def image_to_text(image):
    text = pytesseract.image_to_string(image)
    return text

# 将文本保存到文件
def save_text_to_file(text, output_file):
    with open(output_file, 'w', encoding='utf-8') as file:
        file.write(text)

# 从 PDF 提取文本
def extract_text_from_pdf(input_file, output_file):
    # 将 PDF 转为图像
    images = pdf_to_image(input_file)
    
    extracted_text = ""
    
    # 提取每个图像中的文本
    for image in images:
        text = image_to_text(image)
        extracted_text += text + "\n"
    
    # 将提取的文本保存到文件
    save_text_to_file(extracted_text, output_file)

# 从扫描的 PDF 中提取文本
extract_text_from_pdf('scanned_document.pdf', 'extracted_text.txt')

print("OCR（光学字符识别）魔法完成！现在你可以将扫描的 PDF 文档转换为可编辑的文本了！")
```
哇哦，现在你已经是 OCR 的大魔法师了！让 Python 为你解读那些扫描的 PDF 文件吧！
#### 运行环境依赖问题
问题一：pdf2image.exceptions.PDFInfoNotInstalledError: Unable to get page count. Is poppler installed and in PATH?
您遇到了一个名为 `PDFInfoNotInstalledError` 的错误，提示无法获取页面数量。这通常是因为缺少 Poppler 工具或无法找到它的安装路径。
`pdf2image` 库依赖于 Poppler 工具来处理 PDF 文件。请按照下面的步骤来安装 Poppler 并将其添加到系统的 PATH 环境变量中：

1. 如果您使用的是 macOS 系统，并且已经安装了 Homebrew，可以运行以下命令来安装 Poppler：

brew install poppler

1. 如果您使用的是 Windows 系统，可以前往 Poppler 的官方网站（[https://poppler.freedesktop.org/](https://link.segmentfault.com/?enc=YfqyH6FOThin3GKDZ4q0xg%3D%3D.Gau60vNi7TWoZ8qsnXO55JCP8hNIo8ugakqrfcVGHa0AAxxYWhqvNanuIpGf8TKn)）下载预编译的二进制文件，然后解压到一个目录中。将该目录添加到系统的 PATH 环境变量中。请注意，您可能需要重启终端或编辑器，以便使环境变量的更改生效。
2. 如果您使用的是 Linux 系统，可以使用系统的包管理器来安装 Poppler。具体的安装命令可能因 Linux 发行版而异。例如，在 Ubuntu 上，可以运行以下命令进行安装：

sudo apt-get install poppler-utils
安装完成后，您可以尝试重新运行您的脚本，看看是否还会遇到相同的错误。
问题二：pytesseract.pytesseract.TesseractNotFoundError: tesseract is not installed or it's not in your PATH. See README file for more information.
您遇到了一个名为 `TesseractNotFoundError` 的错误，提示未找到 tesseract 或者它不在您的 PATH 环境变量中。
Pytesseract 是一个使用 Tesseract OCR 引擎的 Python 库。要解决这个问题，您需要执行以下步骤来安装和配置 Tesseract：

1. 您可以从 Tesseract 的官方 GitHub 存储库（[https://github.com/tesseract-ocr/tesseract](https://link.segmentfault.com/?enc=9JLt6kREHMnfvIQlGBOYzQ%3D%3D.1loNOK5%2Bf9JrYN7cl2oH55Ps%2FzUNiIo3qEJolyHQB7w%2FE01wn%2BhQCwLVQijEmAZk)）下载适用于您的操作系统的预构建版本。请确保根据您的操作系统选择正确的版本进行下载。
2. 安装 Tesseract。对于 macOS 和 Windows 用户，下载后的安装程序应该会自动进行安装。对于 Linux 用户，您可以通过包管理器进行安装。
3. 确保 Tesseract 已经添加到系统的 PATH 环境变量中。这样才能在命令行中直接访问 Tesseract 命令。
4. 在终端或命令提示符中运行以下命令，验证是否已成功安装并添加到 PATH 环境变量：

tesseract --version
您应该能够看到 Tesseract 的版本信息。

1. 如果您安装的是 macOS 或 Linux 系统上的 Tesseract，您可能还需要安装对应的语言数据包，以便正确识别不同语言的文本。可以通过 Tesseract 官方存储库提供的语言数据包进行下载和安装。

安装完 Tesseract 并将其添加到 PATH 环境变量后，您可以重新运行您的脚本，看看是否还会遇到相同的错误。
### 总结
太棒了！我们刚刚掌握了 Python PDF 自动化处理的七项魔法！PDF 解析和文本提取、PDF 合并和拆分、PDF 表单处理、PDF 文档转换、PDF 水印和签章、PDF 报表生成以及 OCR（光学字符识别）。相信你已经成为了一个真正的办公室大魔法师！不管是处理文件、生成报表还是提取文本，Python 都可以助你一臂之力。
希望这些有趣的知识点加上幽默的讲解让你爱上了 Python PDF 自动化魔法世界。如果你有任何问题，或者想要获取更多关于这些魔法的中文代码示例，请随时向我提问！让我们继续探索 Python 的无尽可能性吧！
**欢迎关注微信公众号【千练极客】，尽享更多干货文章！**
**欢迎关注微信公众号【千练极客】，尽享更多干货文章！**
**欢迎关注微信公众号【千练极客】，尽享更多干货文章！**
> 本文由博客一文多发平台 [OpenWrite](https://link.segmentfault.com/?enc=9%2BGJh7O09Ort370j8ckKZA%3D%3D.U0PfUCWvrW3TtqCxpjOL1wyaOfGMnpQfBN6zLfJPDBhJIVzJLqyuVJH54YwOUYeN)
>  发布！



> 来自: [乐趣Python——办公魔法：PDF自动化处理 - 个人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000044777848)

