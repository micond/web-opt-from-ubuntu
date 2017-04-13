## Website Performance Optimization project

Goal of the project is to optimize the critical rendering path and make the page render as quickly as possible.
[Critical Rendering Path course](https://www.udacity.com/course/ud884).

## Installation
1. Download the GitHub zip file or clone the repository onto your local workstation:
	* zip file [https://github.com/micond/web-opt-from-ubuntu/archive/master.zip](https://github.com/micond/web-opt-from-ubuntu/archive/master.zip "download zip file")
	* git clone [https://github.com/micond/web-opt-from-ubuntu.git](https://github.com/micond/web-opt-from-ubuntu.git "git clone repository")
2. To run the application please open index.html in your web browser
Application is responsive and can be used on various devices (desktop pc, mobiles, tablets, ...).

Some useful tips to get started:

1. Check out the repository
1. To inspect the site on your phone, you can run a local server

  ```bash
  $> cd /path/to/project-folder
  $> python -m SimpleHTTPServer 8080
  ```

1. Open a browser and visit localhost:8080
1. Download and install [ngrok](https://ngrok.com/) to the top-level of your project directory to make your local server accessible remotely.
2. Visit Getting started with ngrok for more info" [https://ngrok.com/docs#getting-started](https://ngrok.com/docs#getting-started "Gettingt Started")

  ``` bash
  $> cd /path/to/your-project-folder
  $> ./ngrok http 8080
  ```

1. Copy the public URL ngrok gives you and try running it through PageSpeed Insights! Optional: [More on integrating ngrok, Grunt and PageSpeed.](http://www.jamescryer.com/2014/06/12/grunt-pagespeed-and-ngrok-locally-testing/)

### My steps to optimize the web page:

- moved the scripts to the bottom of the page to load them alast and used async to load them asynchronously.
- css inline to not block the page rendering and added backface-visibility: hidden; for .mover class. more info: https://www.w3schools.com/cssref/css3_pr_backface-visibility.asp
- compressed images in Gimp

Best achieved score by PSI: 92/100.

#### Frames per Second in main.js

- I have removed the determineDx function and replace it with below code:

```javascript
  function changePizzaSizes(size) {
      var newWidth = 0;
        switch (size) {
            case "1":
                newWidth = 25;
                break;
            case "2":
                newWidth = 33.3;
                break;
            case "3":
                newWidth = 50;
                break;
            default:
                console.log("bug in sizeSwitcher");
        }

        var randomPizzas = document.querySelectorAll(".randomPizzaContainer");

        for (var i = 0; i < randomPizzas.length; i++) {
            randomPizzas[i].style.width = newWidth + "%";
        }
    }

    changePizzaSizes(size);
```
- In updatePositions() function I have moved calculation Math.sin() outside the loop
    and put it into phase variable. see the code below:
```javascript
function updatePositions() {
  frame++;
  var phase;
  phase = Math.sin((document.body.scrollTop / 1250));
  window.performance.mark("mark_start_frame");

  var items = document.getElementsByClassName('mover');
  for (var i = 0; i < items.length; i++) {
    // phase = Math.sin((document.body.scrollTop / 1250) + (i % 5));
    items[i].style.left = items[i].basicLeft + 100 * (phase + (i % 5)) + 'px';
  }
```
- In addEventListener on line 541, I have removed the querySelector outside the loop, changed it to getElementById and put it into variable miniPizzas. 
- Lowering the amount of pizza element generated from 200 to 40. code below:

```javascript
var miniPizzas = document.getElementById("movingPizzas1");
// Generates the sliding pizzas when the page loads.
document.addEventListener('DOMContentLoaded', function() {
    var cols = 8;
    var s = 256;
    var elem;
    for (var i = 0; i < 40; i++) {
        elem = document.createElement('img');
        elem.className = 'mover';
        elem.src = "images/pizza.png";
        elem.style.height = "100px";
        elem.style.width = "73.333px";
        elem.basicLeft = (i % cols) * s;
        elem.style.top = (Math.floor(i / cols) * s) + 'px';
        miniPizzas.appendChild(elem);
    }
    updatePositions();
});
```
