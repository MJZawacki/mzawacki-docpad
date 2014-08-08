##About

This is the source for the [www.mjzawacki.com](http://www.mjzawacki.com/) which is based off the website of [Erv Walter](http://www.ewal.net/). It is set up to deploy directly to Azure where the web site is automatically configured and deployed.

To learn more about the evolution of the code behind this site, check out [How YTechie uses DocPad](http://www.ytechie.com/2013/11/blogging-awesomeness-with-a-static-generator-and-markdown/) and [Deploying DocPad on Azure](http://www.ewal.net/2013/10/10/deploying-docpad-sites-to-azure/).


###Installation

1. Download the source
2. Run *npm install* in the folder
3. Customize src\partials\me.html
4. Replace your photo in src\files\images\Headshot-Square.jpg (100x100px)
5. Customize the default page template
 * Change the Google Analytics code to your own
 * Change the Google Plus information to your own
 * Change the feed title
 * Change the meta description
 * Change the twitter and github info
 * Change the search site
 * Change copyright
6. Customize the rewrites in src\files\web.config (if using IIS, and only if needed)


#### License

Portions Copyright (c) 2014 [Michael Zawacki](http://www.mzawacki.com/)

Portions Copyright (c) 2013 [Jason Young](http://www.ytechie.com/)

Portions Copyright (c) 2013 [Erv Walter](http://www.ewal.net/)

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
