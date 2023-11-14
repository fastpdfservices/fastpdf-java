# FastPDF Java Package

Welcome to **fastpdf** Java Package, the versatile and quick solution for your PDF rendering needs. This package is a SDK for fastpdfservice.com REST API.

## Overview

`fastpdf` is a Java package designed to simplify the PDF generation process. It streamlines the procedure of rendering HTML/CSS templates into a PDF document, offering granular control over the rendering options, and provides the ability to add images and barcodes into your PDFs.

`fastpdf` leverages the PDF manipulation API available at [fastpdfservice.com](https://fastpdfservice.com), ensuring high performance, scalability, and reliability. By using fastpdf, you are able to generate complex, high-quality PDFs efficiently. 

## Key Features

1. **HTML/CSS to PDF**: Render your HTML/CSS templates into a PDF with ease and precision.
2. **Header/Footer Management**: Easily manage the header and footer of your documents with flexible rendering options.
3. **Barcode & Image Integration**: Seamlessly integrate images and barcodes into your PDFs.
4. **Render Options**: Advanced render options allow granular control over your output. Control aspects like margins, orientation, scales, etc.
5. **Image Mode Selection**: fastpdf supports various image modes to tailor the image rendering to your needs.

## Getting Started

Before you start with the fastpdf Java Package, make sure you have Java 14+ installed on your machine.

To use `fastpdf`, you'll first need to register an account to [fastpdfservice.com](https://fastpdfservice.com), and get your API key. 

Install fastpdf from Maven Central:
```html {{ title: 'Java' }}
<!-- https://mvnrepository.com/artifact/com.fastpdfservice/fastpdf -->
<!-- Add this to your pom.xml file -->
<dependency>
    <groupId>com.fastpdfservice</groupId>
    <artifactId>fastpdf</artifactId>
    <version>1.0.0</version>
</dependency>
```

Import the package in your Java file:

```Java
import com.fastpdfservice.fastpdf.api.PDFClient;
```

To start with, make sure you have your FastPDF service API token handy. 
This is required for all API calls to the FastPDF service. You can find your token under API settings in 
your [FastPDF profile](https://fastpdfservice.com/profile/api).

<CodeGroup title="Register your token">

```Java {{ title: 'Java' }}
PDFClient client = new PDFClient("API_KEY");
```

</CodeGroup>


Please refer to the [documentation](https://docs.fastpdfservice.com) for more details on the available features.


## Creating a Basic Template

Creating a basic template is simple. Let's start by creating an HTML document with a placeholder for a `name` variable.
Then, you'll need to create a [Template](https://docs.fastpdfservice.com/templates#template) object and set `name` and `format`.


<CodeGroup title="Creating a basic template">

```java {{ title: 'Java' }}
import com.fastpdfservice.fastpdf.api.Template;

String template_str = "<html><body>Hello world, my name is {{name}}!</body></html>";
Template template_data = new Template("basic-document", "html");
Template template = client.addTemplate(template_str.getBytes(), template_data);
```

</CodeGroup>

<Note>
  *Make sure you do not forget to call `getBytes()` on your String. Otherwise, the string will be interpreted as a filename.*
</Note>

In this template, `{{name}}` is a placeholder that will be replaced by a real value when generating a PDF. 

## Your First Rendering

Now, let's bring our document to life. We've got our template id handy, and our data ready to be cast into the chosen template. 
But don't worry if you've misplaced the template id - a quick detour to your [template dashboard](https://fastpdfservice.com/services/templates) will point you right to it.
In our data object, we set a value for the `name` variable that we defined in our template.

<CodeGroup title="Rendering our basic template">

```java {{ title: 'Java' }}
Map<String, Object> document_data = new HashMap<>();
document_data.put("name", "John");

String template_id = template.getId();
byte[] document = client.renderTemplate(template_id, document_data);
```

</CodeGroup>

Finally, we save it to the disk using the `save()` convenience function.

<CodeGroup title="Saving our document to the disk">

```java {{ title: 'Java' }}
client.save(document, "path/to/basic-document.pdf");
```

</CodeGroup>

And that's it! You've just brought your first PDF to life.


## For Loops

FastPDF allows for more complex templates. For instance, you can use control structures like for loops and if statements in your templates.
Here is how to iterate over a list of items, using a for loop.

<CodeGroup title="For Loop">

```java {{ title: 'Java' }}
// If you're working with a version of Java prior to Java 15, 
// you'll need to format multi-line strings differently. 
String template_content = """
<html>
<body>
    <ul>
    {% for item in items %}
        <li>{{ item }}</li>
    {% endfor %}
    </ul>
</body>
</html>
""";
Template template_data = new Template("for-loop-document", "html");
Template template = client.addTemplate(template_content.getBytes(), template_data);
```

</CodeGroup>

In this template, `{% for item in items %}` starts the loop, and `{% endfor %}` ends it.

Next, we define our rendering_data by associating a list of item value to the `items` key, and render our document.

<CodeGroup title="Rendering our For Loop template">

```java {{ title: 'Java' }}
Map<String, Object> document_data = new HashMap<>();
document_data.put("items", List.of("First item", "Second item", "Last item"));

String template_id = template.getId();
byte[] document = client.renderTemplate(template_id, document_data);
// Save to disk, if needed
client.save(document, "path/to/loop-document.pdf");
```

</CodeGroup>


## If Statements

If statements can be used to conditionally include parts of the template, based on the rendering data. Here's an example:

<CodeGroup title="If statement">

```java {{ title: 'Java' }}
String template_content = """
<html>
<body>
    {% if user_premium %}
        <p>Welcome, premium user!</p>
    {% else %}
        <p>Welcome, regular user!</p>
    {% endif %}
</body>
</html>
""";
Template template_data = new Template("if-document", "html");
Template template = client.addTemplate(template_content.getBytes(), template_data);
```

</CodeGroup>

In this example, `{% if user_premium %}` starts the if statement, and `{% endif %}` ends it. 
If `user_premium` is true, the "Welcome, premium user!" message will be included in the generated PDF. 
Otherwise, the "Welcome, regular user!" message will be included.

## Stylesheets

<Note>
    *Please ensure that your stylesheets are written purely in CSS. As of now, FastPDF does not support precompiled CSS languages 
    like SCSS, or CSS frameworks like Tailwind. 
    Stick to standard CSS to ensure compatibility and successful rendering.*
</Note>

You can do this in one of two ways: by using a separate stylesheet, or by using inline CSS. 

The latter works as usual, by either
setting the `style` property html tags, or by writing your css classes in the header of the template:

<CodeGroup title="A template with style">

```html {{ title: 'HTML' }}
<html>
<head>
    my-class {
        color: #f43f54;
        font-size: 12px;
        font-weight: bold;
    }
</head>
<body>
    <p class='my-class'>Welcome, {{name}}!</p>
    <p style='color: #ccc'> Goodbye ! </p>
</body>
</html>
```

</CodeGroup>

Alternatively, you can use the `add_stylesheet()` method to add a stylesheet to your template:

<CodeGroup title="A template with style">

```java {{ title: 'Java' }}
import com.fastpdfservice.fastpdf.api.StyleFile;

String stylesheet_path = "styles.css";
StyleFile stylesheet_data = new StyleFile("css");

String template_id = template.getId();
client.addStylesheet(template_id, stylesheet_path, stylesheet_data);
```

</CodeGroup>

Your template will now be rendered with style !

## Adding an image

FastPDF allows you to add images to your templates, which can then be rendered into your final PDF documents. Here's how you can accomplish this.

To begin, we create our template with an `<img>` tag, using our image uri in the `src` property as followed:

<CodeGroup title="Creating a template with an image">

```java {{ title: 'Java' }}
String template_content = """
<html>
<body>
    <img src="{{my_favourite_logo}}">
    <p>Welcome, {{name}}!</p>
</body>
</html>
""";
Template template_data = new Template("image-document", "html");
Template template = client.addTemplate(template_content.getBytes(), template_data);
```

</CodeGroup>

Then, we add an image to our template, and set the same image uri. 
This step can be done from your [template dashboard](https://fastpdfservice.com/services/templates).

<CodeGroup title="Adding an image">

```java {{ title: 'Java' }}
import com.fastpdfservice.fastpdf.api.ImageFile;

String image_path = "my-logo.png";
ImageFile image_data = new ImageFile("png", "my_favourite_logo");

String template_id = template.getId();
ImageFile image = client.addImage(template_id, image_path, image_data);
```

</CodeGroup>

Finally, we render our document. As usual, we define our rendering data object with our variable. 

<CodeGroup title="Rendering our image template">

```java {{ title: 'Java' }}
Map<String, Object> document_data = new HashMap<>();
document_data.put("name", "Jane");

String template_id = template.getId();
byte[] document = client.renderTemplate(template_id, document_data);
// Save to disk, if needed
client.save(document, "path/to/image-document.pdf");
```

</CodeGroup>

## Custom Header and Footer

By default, FastPDF will include a header, composed of the document title and the date, and a footer with page numbers. 
You can turn them on and off separately when adding your [Template](https://docs.fastpdfservice.com/templates#template) or during rendering with the 
[RenderOptions](https://docs.fastpdfservice.com/render#render-options).

It is also possible to add custom headers and footers to your PDF documents. These headers and footers can include images, and
dynamic content such as the current page number, total number of pages, and the current date. Let's take a look at how to do this.

Firstly, let's create a new template:

<CodeGroup title="A simple template">

```java {{ title: 'Java' }}
String template_content = """
<html>
<body>
    <h1>{{title}}</h1>
    <p>{{content}}</p>
</body>
</html>
""";
Template template_data = new Template("customer-header-footer-document", "html");
```

</CodeGroup>

You might have noticed that we have not added the template yet. It is because we need to define our header and footer first.
Headers and footers comes with [special HTML classes](https://docs.fastpdfservice.comtemplates#header-and-footer) that can be used to inject printing values into them.
We will use them to set the page number.


<CodeGroup title="Custom header and footer">

```java {{ title: 'Java' }}
String header_content = """
<div style="text-align: center; padding: 10px; font-size: 8px;">
    <h2>{{header_note}}</h2>
</div>
""";
String footer_content = """
<div style="text-align: center; margin: auto; padding: 10px; font-size: 8px;">
    <span class="pageNumber"></span>
</div>
""";
Template template = client.addTemplate(template_content.getBytes(), template_data,
                                       header_content.getBytes(), footer_content.getBytes());
```

</CodeGroup>

Now, when you render the PDF, pass the `header_note`, `title` and `content` in your data:

<CodeGroup title="Rendering custom header document">

```java {{ title: 'Java' }}
Map<String, Object> document_data = new HashMap<>();
document_data.put("title", "My document");
document_data.put("content", "My document content");
document_data.put("header_note", "This is my favorite header");

String template_id = template.getId();
byte[] document = client.renderTemplate(template_id, document_data);
// Save to disk, if needed
client.save(document, "path/to/custom-header-document.pdf");
```

</CodeGroup>


## Generating Multiple PDFs

The FastPDF API also provides the ability to generate multiple PDFs in a single API call. This feature, referred to as batch processing, can be useful in situations where you need to generate many PDFs with different data.

The way batch processing works in FastPDF is that you provide an array of data objects, each of which corresponds to one PDF. Let's see how to do this in practice.

First, let's prepare our template and data. Suppose we want to generate PDFs for each user in our system.

<CodeGroup title="Basic template">

```java {{ title: 'Java' }}
String template_content = """
<html>
<body>
    <h1>Welcome, {{name}}!</h1>
    <p>Email: {{email}}</p>
</body>
</html>
""";
Template template_data = new Template("image-document", "html");
Template template = client.addTemplate(template_content.getBytes(), template_data);
```

</CodeGroup>

Here, the template contains placeholders for user data (name and email). Next, we will prepare our data array.
Each element of the array corresponds to one document. Here we will render 3 documents.

<CodeGroup title="List of data objects">

```java {{ title: 'Java' }}
List<Map<String, Object>> users = new ArrayList<>();
Map<String, Object> user1 = new HashMap<>();
user1.put("name", "John Doe");
user1.put("email", "john@example.com");
users.add(user1);
Map<String, Object> user2 = new HashMap<>();
user2.put("name", "Jane Doe");
user2.put("email", "jane@example.com");
users.add(user2);
Map<String, Object> user3 = new HashMap<>();
user3.put("name", "Richard Roe");
user3.put("email", "richie@example.com");
users.add(user3);
```

</CodeGroup>

Finally, we can call the `render_template_many()` method with our template id and batch data.

<CodeGroup title="Rendering of multiple Documents">

```java {{ title: 'Java' }}
String template_id = template.getId();
byte[] zip_result = client.renderTemplateMany(template_id, users);
```

</CodeGroup>

The `render_template_many()` method will return a zip file that contains the 3 PDFs. 
You now have the choice between extracting the zip_result into a Java List, or in a directory on your disk, both using the `extract()` method.

<CodeGroup title="Extracting result PDFs">

```java {{ title: 'Java' }}
// Save all pdfs to the disk
client.extract(zip_result, "path/to/directory/");

// Or get a list of PDF
List<byte[]> pdfs = client.extract(zip_result);
```

</CodeGroup>



## Support

For any questions or support, reach out to us at `support@fastpdfservice.com`.

## License

fastpdf Java Package is licensed under the MIT license. See LICENSE for more details.


