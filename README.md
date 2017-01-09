## Project 4 - Website Optimization

This Project resides at: https://gitlab.com/lglaves/website-optimization and will be hosted at: http://www.lesliebeth.com temporarily.

This is Project 4 for the Udacity Front End Developer Nanodegree. Goals for this project were to optimize the website for Cameron Pittman's Portfolio to meet the following performance metrics:

1. **Critical Rendering Path:** Improve index.html to achieve a PageSpeed score of at least 90 for Mobile and Desktop views.

2. **Frame Rate:** Optimize main.js to enable the view pizza.html to render at a consistent frame-rate of 60fps when scrolling.

3. **Computational Efficiency:** Resizing pizzas must take less than 5ms using the pizza size slider on the pizza.html page.

4. **README:** Include instructions on how to sucessfully run the application and details of all steps taken to meet the optimization criteria.

5. **Comments:** Code comments should effectively explain any longer code procedures.

### Changes Made to the Project
1. I researched and initially selected the Brunch.io static site generator to use for hosting the site on my GitLab Pages.
2. When I found that it is impossible to use .htaccess to leverage browser caching on GitLab hosted Pages, I hosted the project website at my own registered domain, lesliebeth.com for testing.
3. I reorganized directories and files to work with Brunch.io.  Benefits claimed for using Brunch are small and simple config files, 3 simple commands (brunch new, brunch build, brunch watch), and more.
4. After encountering numerous issues and problems working with Brunch.io, I changed the project to make use of a gulp.js build system.

### Changes Made to Optimize index.html
1. Installed [ngrok](https://ngrok.com/) to serve the website from localhost to the internet for initial testing.
    * Run from windows: ngrok http 80
    * Run from mac: 
2. Made all script calls asynchronous.
3. **Images:** 
    * After seeking a plugin for Brunch.io that would automatically process images and installing one that failed to work [imageoptimizer-brunch](https://github.com/steffenmllr/imageoptmizer-brunch/issues/13), I resized the pizzeria image using IrfanView and then used Tiny PNG to compress all images in the project.
    * In my second revision of the project, using a gulp build system, images were further optimized using gulp-imagemin.
    * Google PageSpeed Insights additionally minimized a few of the images even further, and I put those in my app/images folder.
4. Inlined css style and print for index.html **Note:** Original files had one style.css for index.html and another style.css for the pages in /views (the style for index.html is now inlined to load the first page faster).
5. Tested site speed using [keycdn](https://tools.keycdn.com/speed) and found that loading the google web fonts took 36ms.  I found that the Google fonts used seem to be a default and the website will display the same fonts without linking to fonts.googleapis.com.
6. Wrote an .htaccess file to enable html caching for my Dreamhost server account, enable gzip compression and set expiration times on cached files.
  [tweaking-htaccess-for-website-performance-on-dreamhost](http://www.mark-leong.com/tweaking-htaccess-for-website-performance-on-dreamhost/)
  
### Optimization Results for index.html
* Page Speed Insights - Achieved Desktop Pagespeed score of 98/100
* Page Speed Insights - Achieved Mobile Pagespeed score of 98/100 
* Unresolved Issue - Did not implement any solution for the Page Speed Insights suggested improvement to "Leverage browser caching for the following cacheable resources" 
  http://www.google-analytics.com/analytics.js (2 hours)
  There are several complex solutions, but I am not pursuing it for this project.

### Results After Optimizing main.js for pizza.html 
Chrome:
* Time to generate pizzas on load ranges from 27 to 45ms
* Avg scripting time to generate last 10 frames is typically <1ms
* Time to resize pizzas is <1ms, sometimes longer on first resize but still <2ms
* Frame Rate Meter seems to indicate a consistent 59-61 fps when run with Paint Flashing and Layer Borders
Firefox:
* Time to generate pizzas on load ranges around 63ms
* Avg scripting time to generate last 10 frames is typically <1ms, sometimes the first 10 frames take longer, up to 2ms
* Firefox shows a TypeError for 'timeToResize'
* Firefox displays a warning about scroll-linked positioning effect. Scrolling does not move the random pizzas, most likely due to asynchronous panning used in Firefox.

### Changes Made to main.js

1. **updatePositions()** Moved computations outside of the for loop and changed document.querySelectorAll to use document.getElementsByClassName. Changed from using style.left to using style.transform on the pizza positions. (~line 504) 

   **Result:** This reduced the time to generate the last 10 frames down to <1ms.
   
2. **Resized the pizza.png** used for moving pizzas down to 100px by 100px, this enabled removing the resizing of the image from the pizza generating loop in document.addEventListener. 

   **Result:** This did not seem to improve anything, but this reduced the size of the image from 18.1kB to 5.7kB.
   
3. **Reduced the number of sliding pizzas** from 200 down to 35. (~line 543)

   **Result:** This did not reduce the first generation of 10 frames, but subsequent generation of 10 frames was now <1ms.  Viewing the page on a large desktop screen, 1920 x 1080 pixels did not exhibit any noticeable lack of sliding pizzas.
   
4. **pizzasDiv** Moved computation outside of the for loop that creates and appends pizzas (pizzasDiv). (~line 469)

5. **changePizzaSizes(size)** Moved computations outside of the for loop to reduce the time to resize pizzas. (~line 438)

    **Result:** This reduced the time to resize pizzas down to <2ms.
    
6. **document.addEventListener('DOMContentLoaded', function()** Removed height and width comps, they are not needed with the resized square image

7. Added 'use strict' to the top of the file.
 

### References for Optimizing the Website
* [ngrok](https://ngrok.com/)
* [IrfanView](http://www.irfanview.com/)
* [Tiny PNG and JPEG Compression](https://tinypng.com/)
* [brunch-guide-demos](https://github.com/brunch/brunch-guide-demos)
* [Will Google Fonts Slow Down My Page?](https://developers.google.com/fonts/faq#will_web_fonts_slow_down_my_page)
* [Animation Loops Using requestAnimationFrame](https://www.kirupa.com/html5/animating_with_requestAnimationFrame.htm)
* [Firefox Developer Tools](https://developer.mozilla.org/en-US/docs/Tools/Performance/Frame_rate#Frame_rate_and_responsiveness)
* [Silky CSS: Minimizing Repaints & Jank](https://trendyminds.com/blog/silky-css-minimizing-repaints-jank)

### Brunch.io Build (Abandoned this build on 01-04-17)

####  Notes About Brunch.io
1. app directory is for development and public directory is for deployment files
2. app/assets files are copied directly to public
3. Brunch.io watches app, vendor and test directories for changes and can serve your website and automatically show changes by using **brunch watch --server** from the command line of the project directory
4. Stylesheets and Script files in the app directory and vendor directories are typically concatenated and minified based on details in the brunch.config file
5. To build for deployment, use **brunch build --production** from the command line of the project directory

#### Building Locally Using Brunch.io
To work locally with this project, follow the steps below:
1. Fork, clone or download a zip file for this project
2. Install Node.js
3. Install Brunch: npm install -g brunch
4. Install Brunch plugins and app dependencies: npm install
5. Generate the website: brunch build --production
6. Preview your project: brunch watch --server

## Gulp Build System

### Building Locally Using Gulp
1. Fork, clone or download a zip file for this project
2. Install Node.js
3. Install Gulp: npm install -g gulp
4. Install Gulp plugins and app dependencies from command line in root folder of unzipped project using: npm install
5. This gulp build system makes use the following commands:
    * gulp serve to open project
    * gulp to build project into /dist folder
    * gulp pagespeed to test live project speed
    * gulp serve:dist to serve production version from /dist folder
6. To install on server, copy /dist folder contents to the root directory of your website

### Continuous Deployment Goal (Future)
**To Be Done:** Modify gulp.js to automatically deploy updates to my server on DreamHost for other projects.

### References for Gulp Build System
* [Optimise Your Web Design Workflow With Gulp](https://esksidedesign.co.uk/blog/optimise-your-web-design-workflow-with-gulp/)
* [CSS-Tricks, Gulp for Beginners](https://css-tricks.com/gulp-for-beginners/)
* [Automate Your Tasks Easily With Gulp.js](https://scotch.io/tutorials/automate-your-tasks-easily-with-gulp-js)
* [Automatically Load Gulp Plugins with gulp-load-plugins](http://andy-carter.com/blog/automatically-load-gulp-plugins-with-gulp-load-plugins)
* [Building and Deployment with gulp.js and Git](http://samluescher.net/articles/building-and-deployment-gulpjs-and-git/)
* [Using gulp.js to Deploy Code](http://mikeeverhart.net/2016/01/deploy-code-to-remote-servers-with-gulp-js/)
* [Deploying Websites with Git on Dreamhost](https://brandonevans.ca/post/text/deploying-websites-with-git-on-dreamhost/)
* [DreamHost Enabling Shell access](https://help.dreamhost.com/hc/en-us/articles/216385837-Enabling-Shell-access)



### My Dev Env on Mac:
  * Updated 03/04/17:  Node.js v7.3.0 and npm v3.10.10
  * Gulp version 3.9.1
  
### My Dev Env on Windows
  * Node version 7.0.0, npm version 3.10.9
  * Gulp version 3.9.1
 
