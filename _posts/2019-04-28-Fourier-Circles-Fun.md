---
title: Fourier Circles Fun
date: 2019-04-28 09:13:11 
categories: [MATH]
tags: [math]     # TAG names should always be lowercase
description: "Add add add add, random, then run..."
---
<html>
<script src="https://code.jquery.com/jquery-3.4.0.slim.js"
    integrity="sha256-milezx5lakrZu0OP9b2QWFy1ft/UEUK6NH1Jqz8hUhQ=" crossorigin="anonymous"></script>

<body>
    <input type="button" value="Run" class="add" id="run" />
    <input type="button" value="Add a circle" class="add" id="add" />
    <input type="button" value="random" class="random" id="random" />

    <fieldset id="buildyourform">
        <legend>Build your own form!</legend>
    </fieldset>

    <canvas id="myCanvas" width="800" height="1024" style="border:1px solid #d3d3d3;">
        Your browser does not support the HTML5 canvas tag.</canvas>


    <div id="inputs">

    </div>
    <script>
        var step = 0;
        var poly = [];
        params = [];

        function DrawParams(ctx) {
            pos={x:512,y:512};
            for (j = 0; j < params.length; j++) {
                ctx.beginPath();
            ctx.arc(pos.x, pos.y, params[j].r, 0, 2 * Math.PI);
            ctx.strokeStyle = "#FF0000";
            ctx.stroke();
                angle = params[j].freq * time;
                pos.x += Math.sin(angle) * params[j].r;
                pos.y += Math.cos(angle) * params[j].r;
            }
        }
        function Draw() {
            var c = document.getElementById("myCanvas");
            var ctx = c.getContext("2d");
            ctx.clearRect(0, 0, 1024, 1024);
            ctx.beginPath();
            for (i = 0; i < poly.length - 1; i++) {
                p1 = poly[i];
                p2 = poly[i + 1];
                ctx.moveTo(p1.x, p1.y);
                ctx.lineTo(p2.x, p2.y);
            }
            ctx.strokeStyle = "#000000";
            ctx.stroke();
            DrawParams(ctx);

        }
        $(document).ready(function () {
            $("#add").click(function () {
                var lastField = $("#buildyourform div:last");
                var intId = (lastField && lastField.length && lastField.data("idx") + 1) || 1;
                var fieldWrapper = $("<div class=\"fieldwrapper\" id=\"field" + intId + "\"/>");
                fieldWrapper.data("idx", intId);
                var fRadius = $("<input type=\"text\"  class=\"radius\" />");
                var fFrequency = $("<input type=\"text\" class=\"frequency\" />");
                var removeButton = $("<input type=\"button\" class=\"remove\" value=\"-\" />");
                removeButton.click(function () {
                    $(this).parent().remove();
                });
                fieldWrapper.append(fRadius);
                fieldWrapper.append(fFrequency);
                fieldWrapper.append(removeButton);
                $("#buildyourform").append(fieldWrapper);
            });
            $("#random").click(function () {

                $(".fieldwrapper").each(function (i, v) {
                    radius = (jQuery(v).children(".radius")[0].value = Math.random() * 50);
                    freq = (jQuery(v).children(".frequency")[0].value = Math.random() * 400 - 200);

                });
            });
            $("#run").click(function () {

                $(".fieldwrapper").each(function (i, v) {
                    radius = parseFloat(jQuery(v).children(".radius")[0].value);
                    freq = parseFloat(jQuery(v).children(".frequency")[0].value);
                    params[i] = { r: radius, freq: freq, angle: 0 };
                });




            });
        });

        function Record(pos) {
            poly[poly.length] = pos;
            if (poly.length > 100) {
                poly.shift();
            }
        }
        function Tick() {
            timestep = 0.001;
            time = timestep * step;
            step++;
            pos = { x: 0, y: 0 }
            for (j = 0; j < params.length; j++) {
                angle = params[j].freq * time;
                pos.x += Math.sin(angle) * params[j].r;
                pos.y += Math.cos(angle) * params[j].r;
            }
            pos.x += 512;
            pos.y += 512;
            Record(pos);
            window.requestAnimationFrame(Draw);
        }
        setInterval(Tick, 100);
        
    </script>

    <p><strong>Note:</strong> The canvas tag is not supported in Internet
        Explorer 8 and earlier versions.</p>

</body>
<script> fetch('https://fancyzero.com/pvcounter/'+encodeURIComponent(window.location.pathname), { method: 'GET' });</script>
</html>
