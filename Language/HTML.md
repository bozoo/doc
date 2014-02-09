HTML
====

```html
<!DOCTYPE html>
<html>
     <head>
          <title>This my title</title>
     </head>
     <body>
         <h1>This is my heading</h1>
<!-- Different headings from h1 to h6 -->
         <p align="center">This is a <em>paragraph</em></p>
         <img src="http://www.example.com/image.jpeg" />
         <a href="http://www.example.com/link.html>This is a link</a>
<!-- Ordered List -->
         <ol>
              <li>1st <strong>item</strong></li>
              <li>2nd item</li>
         </ol>
<!-- Unordered List -->
         <ul>
              <li>1st item</li>
              <li>2nd item</li>
         </ol>
     </body>
</html>

Style:

   * font-size: 14px

   * color: orange

   * font-family: Bodoni

   * background-color: blue


Tables:

<html>
    <head>
        <title>Table Time</title>
    </head>

    <body>

        <table style="border-collapse:collapse;">
            <thead>
                <tr>
                    <th colspan="2">Famous Monsters by Birth Year</th>
                </tr>
                <tr style="border-bottom:1px solid black;">
                    <th style="padding:5px;color:red"><em>Famous Monster</em></th>
                    <th style="padding:5px;border-left:1px solid black;color:red"><em>Birth Year</em></th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td style="padding:5px;">King Kong</td>
                    <td style="padding:5px;border-left:1px solid black;">1933</td>
                </tr>

                <tr>
                    <td style="padding:5px;">Dracula</td>
                    <td style="padding:5px;border-left:1px solid black;">1897</td>
                </tr>

                <tr>
                    <td style="padding:5px;">Bride of Frankenstein</td>
                    <td style="padding:5px;border-left:1px solid black;">1944</td>
                </tr>
            </tbody>
        </table>

    </body>


</html>

Div et Span:
          <div style="width:50px; height:50px; background-color:green"></div>
          <a href="http://www.google.com">
              <div style="width:50px; height:50px; background-color:yellow"></div>
         </a>

          <p>This text is black, except for the word <span style="color:red">red</span>!</p>
          <p>My favorite font is <span style="font-family:Futura">Futura</span>!</p>

CSS:

<link type="text/css" rel="stylesheet" href="stylesheet.css"/>

index.html
<!DOCTYPE html>
<html>
     <head>
         <link type="text/css" rel="stylesheet" href="stylesheet.css"
          <title>Result</title>
     </head>
     <body>
         <h1>Hello!</h1>
         <p>Welcome to my page</p>
         <img src="http://hearstcommerce.ca/customcontent/members/premium/sample.jpg" />
         <p>
             <a href="http://www.google.com">Find me on Google</a>
         </p>
     </body>
</html>

stylesheet.css
h1 {
    font-family: Verdana, sans-serif;
    color: #576D94;
}


p {
    font-family: Garamond, serif;
    font-size: 18px;
    color: #4A4943;
}


img {
    height: 100px;
    width: 300px;
    border: 1px solid #4682b4;
}


a {
    text-decoration: none;
    color: #CC0000;
}

 Bouton:

<div>
     <a href="http://www.google.com">Find us on <span>Google!</span></a>
</div>



div {
    height: 50px;
    width: 120px;
    border: #6495ED dashed 2px;
    border-radius: 5px;
    background-color: #BCD2EE;
    margin: auto;
    text-align: center;
}


a {
    color: #6495ED;
    text-decoration: none;
    font-family: Verdana, sans-serif;
    font-size: 18px;
}


span {
    color: white;
}

Class and id:

<div class="square"></div>
<img class="square"/>
<td class="square"></td>

.square {
    height: 100px;
    width: 100px;
}

<div id="first"></div>
<div id="second"></div>
<p id="intro"></p>

#first {
    height: 50px;
}

#second {
    height: 100px;
}

#intro {
    color: #FF0000;
}

Pseudo-class selectors:

selector:pseudo-class_selector {
    property: value;
}

a:link: An unvisited link.
a:visited: A visited link.
a:hover: A link you're hovering your mouse over.

p:first-child {
    color: red;
}

p:nth-child(2) {
    color: red;
}


CSS with header, footer and two columns:
div {
	border-radius: 5px;
}

#header {
	height: 50px;
	background-color: #F38630;
	margin-bottom: 10px;
}

.left {
	height: 300px;
	width: 150px;
	background-color: #A7DBD8;
	float: left;
	margin-bottom: 10px;
}

.right {
	height: 300px;
	width: 450px;
	background-color: #E0E4CC;
	float: right;
	margin-bottom: 10px;
}

#footer {
	height: 50px;
	background-color: #69D2E7;
	/*Add your CSS here!*/
    clear: both;
}
```
