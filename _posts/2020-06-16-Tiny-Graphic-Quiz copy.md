---
title: Tiny Graphic Quiz
date: 2020-06-16 13:52:42 
categories: [MATH]
tags: [math]     # TAG names should always be lowercase
description: "Can you solve it?"
---
<html>
<html lang="en">



<body>
    <canvas id="glcanvas" width="512" height="512"></canvas>
</body>
<p>input correct rotation to get a perfect hexagon projection from a cube ( to the 3rd decimal place ), and green face should visible</p>
<p>
    <label> rotate x:</label>
    <input id="rotationX" value="45.0" />
    <label> rotate y:</label>
    <input id="rotationY" value="45" />
</p>
<p id="annouce"></p>
<script src="/assets/tiny-graphic-quiz/md5.js"></script>
<script src="/assets/tiny-graphic-quiz/gl-matrix.js"></script>
<script src="/assets/tiny-graphic-quiz/webgl-demo.js"></script>
<script>
    //verify answer
    function checkAnswer() {
        strRotX = document.getElementById("rotationX").value;
        x1 = strRotX.substring(0, 6);

        strRotY = document.getElementById("rotationY").value;
        y1 = strRotY.substring(0, 2); 
        if (hex_md5(x1) == "2e701aaec0ad662299588c57cb105034" && hex_md5(y1) == "6c8349cc7260ae62e3b1396831a8398f")
		{
            document.getElementById("annouce").innerHTML = "The answer is correct!"
			document.getElementById("annouce").style.color = "green"
		}
        else
		{
            document.getElementById("annouce").innerHTML = "Current answer is incorrect."
			document.getElementById("annouce").style.color = "red"
			}
        requestAnimationFrame(checkAnswer);
    }
    requestAnimationFrame(checkAnswer);
</script>
<script> fetch('https://fancyzero.cn/'+encodeURIComponent(window.location.pathname), { method: 'GET' });</script>
</html>

