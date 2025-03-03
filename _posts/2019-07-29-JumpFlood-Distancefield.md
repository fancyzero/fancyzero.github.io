---
title: Generate Distance field using Jump Flood
date: 2019-07-29 19:33:52 
categories: [GRAPHICS]
tags: [sdf,graphics]     # TAG names should always be lowercase
---
<html>
    <script lang="javascript">
    
        mapSize = 128
        gridSize = 5
        currentRadius = Math.ceil(mapSize / 2)
        ctxScale = 1;
        mapData = []
        intialMapData = []
    
        class Point {
            constructor(index, row, col) {
                this.idx = index;       //color
                this.siteRow = row;     //from witch site
                this.siteCol = col;     //from witch site
            }
    
        }
        function RandomInt(min, max) {
            min = Math.ceil(min);
            max = Math.floor(max);
            return Math.floor(Math.random() * (max - min + 1)) + min;
        }
    
        function InitializeMap() {
    
            mapData = [];
            for (row = 0; row < mapSize; row++) {
                mapData[row] = []
                for (col = 0; col < mapSize; col++) {
                    mapData[row][col] = new Point(0, -1, -1)
                    if (RandomInt(0, mapSize * mapSize) < 30)
                        mapData[row][col] = new Point(RandomInt(1, 6), row, col);
                }
            }
            intialMapData = CloneMap(mapData);
        }
    
    
    
        var RGBToHex = function (rgb) {
            var hex = Number(rgb).toString(16);
            if (hex.length < 2) {
                hex = "0" + hex;
            }
            return hex;
        };
        function FullColorHex(r, g, b) {
            var red = RGBToHex(r);
            var green = RGBToHex(g);
            var blue = RGBToHex(b);
            return "#" + red + green + blue;
        };
        function ColorFromIndex(idx, highlight = false) {
            m = 1;
            if (highlight)
                m = 2;
            if (idx == 0)
                return FullColorHex(122, 122, 122)
            if (idx == 1)
                return FullColorHex(127 * m, 56 * m, 56 * m)
            if (idx == 2)
                return FullColorHex(56 * m, 127 * m, 56 * m)
            if (idx == 3)
                return FullColorHex(56 * m, 56 * m, 127 * m)
            if (idx == 4)
                return FullColorHex(127 * m, 127 * m, 56 * m)
            if (idx == 5)
                return FullColorHex(56 * m, 127 * m, 127 * m)
            if (idx == 6)
                return FullColorHex(127 * m, 56 * m, 127 * m)
        }
        function GetData(datas, row, col) {
            if (row >= 0 && row < mapSize && col >= 0 && col < mapSize) {
                return datas[row][col];
            }
            else
                return new Point(0, -1, -1);
        }
    
        function CloneMap(src) {
            newData = []
            for (y = 0; y < src.length; y++) {
                newData[y] = src[y].slice(0);
            }
            return newData;
        }
        function ResetMap() {
            mapData = CloneMap(intialMapData);
            currentRadius = Math.ceil(mapSize / 2);
            DrawMap(mapData);
    
        }
    
        function FloodMap(datas, jumpFlooding) {
            dest = CloneMap(datas)
            /*
                O O O
                O x O
                O O O
            */
            offsets = [[-1, -1], [0, -1], [1, -1],[-1, 0], [1, 0],/*[0,0] exclude self*/[-1, 1], [0, 1], [1, 1]];
            for (row = 0; row < mapSize; row++) {
                for (col = 0; col < mapSize; col++) {
                    // if (datas[row][col][0] > 0)
                    //     continue;
                    offsets.forEach(elm => {
                        var multiplier = 1;
                        if (jumpFlooding)
                            multiplier = currentRadius;// [jump flooding] sample point at half mapsize away(distance will reduce every iteration)
                        value = GetData(datas, row + elm[0]*multiplier, col + elm[1]*multiplier);
                        if (value.idx > 0) {
                            if (dest[row][col].idx == 0) {
                                dest[row][col] = value
                            }
                            else {
                                //if dest is alrady colored by other site, compare distance , and override if current site is closer
                                origin = [dest[row][col].siteRow, dest[row][col].siteCol];
                                newOrigin = [value.siteRow, value.siteCol];
                                dist2Origin = (row - origin[0]) * (row - origin[0]) + (col - origin[1]) * (col - origin[1]);
                                dist2NewsOrigin = (row - newOrigin[0]) * (row - newOrigin[0]) + (col - newOrigin[1]) * (col - newOrigin[1]);
                                if (dist2Origin > dist2NewsOrigin)
                                    dest[row][col] = value
                            }
                        }
    
                    });
    
                }
            }
            mapData = dest;
            DrawMap(mapData);
            if (jumpFlooding)
                currentRadius = Math.ceil(currentRadius/2);//reduce radius by half
        }
    
    
        function DrawMap(datas) {
    
            var canvas = document.getElementById('canvas');
    
            if (canvas.getContext) {
    
                var ctx = canvas.getContext('2d');
                ctx.clearRect(0, 0, 10000, 10000);
    
                for (i = 0; i < mapSize; i++) {
                    for (j = 0; j < mapSize; j++) {
                        if (datas[j][i].idx >= 1)
                            ctx.fillStyle = ColorFromIndex(datas[j][i].idx)
                        else
                            ctx.fillStyle = FullColorHex(125, 125, 125);
                        ctx.fillRect(i * (gridSize), j * (gridSize), gridSize, gridSize);
                        ctx.strokeStyle = "#3f3f3f";
                        ctx.strokeRect(i * (gridSize), j * (gridSize), gridSize, gridSize);
                    }
    
                }
    
                // draw highlighted site
                for (i = 0; i < mapSize; i++) {
                    for (j = 0; j < mapSize; j++) {
                        if (intialMapData[j][i].idx >= 1) {
                            ctx.fillStyle = ColorFromIndex(intialMapData[j][i].idx, true/*get hightlight color*/);
                            ctx.fillRect(i * (gridSize), j * (gridSize), gridSize, gridSize);
                            ctx.strokeStyle = "#3f3f3f";
                            ctx.strokeRect(i * (gridSize), j * (gridSize), gridSize, gridSize);
    
                        }
                    }
                }
            }
        }
    
        function OnCanvasMouseScroll(e) {
            var canvas = document.getElementById('canvas');
    
            var ctx = canvas.getContext('2d');
            var e = window.event || e;
            var delta = Math.max(-1, Math.min(1, (e.wheelDelta || -e.detail)));
            var oldScale = ctxScale;
            ctxScale += delta;
            if (ctxScale < 1)
                ctxScale = 1;
            if (ctxScale > 5)
                ctxScale = 5;
            console.debug(ctxScale);
            canvas.width = ctxScale * mapSize * gridSize;
            canvas.height = ctxScale * mapSize * gridSize;
            ctx.transform(ctxScale, 0, 0, ctxScale, 0, 0);
    
            DrawMap(mapData);
    
            event.preventDefault()
    
        }
    
    
    
        function OnCanvasClick(e) {
    
            var canvas = document.getElementById('canvas');
            const rect = canvas.getBoundingClientRect()
            const x = event.clientX - rect.left
            const y = event.clientY - rect.top
            gridX = Math.floor(x / gridSize / ctxScale)
            gridY = Math.floor(y / gridSize / ctxScale)
            startX = (gridX + 0.5) * gridSize;
            startY = (gridY + 0.5) * gridSize;
            DrawMap(mapData);
            var canvas = document.getElementById('canvas');
            var ctx = canvas.getContext('2d');
            ctx.strokeStyle = "#ff7fff";
            closest = []
            for (i = 0; i < mapSize; i++) {
                for (j = 0; j < mapSize; j++) {
                    if (intialMapData[j][i].idx >= 1)
                        closest.push(intialMapData[j][i])
                }
            }
    
            closest.sort(function (a, b) {
    
                endXa = (a.siteCol + 0.5) * gridSize;
                endYa = (a.siteRow + 0.5) * gridSize;
                endXb = (b.siteCol + 0.5) * gridSize;
                endYb = (b.siteRow + 0.5) * gridSize;
                distToA = (startX - endXa) * (startX - endXa) + (startY - endYa) * (startY - endYa)
                distToB = (startX - endXb) * (startX - endXb) + (startY - endYb) * (startY - endYb)
                return distToA - distToB;
            });
            closest = closest.slice(0, 3)
            closest.forEach(pt => {
                ctx.beginPath();
    
                endX = (pt.siteCol + 0.5) * gridSize;
                endY = (pt.siteRow + 0.5) * gridSize;
                ctx.moveTo(startX, startY);
                ctx.lineTo(endX, endY);
                ctx.stroke();
                ctx.font = "10px";
                distance = (startX - endX) * (startX - endX) + (startY - endY) * (startY - endY)
                distance = Math.sqrt(distance);
                ctx.fillText(distance.toFixed(3), endX, endY);
            })
        }
    
        function OnMapSizeChanged(e) {
            slider = document.getElementById("mapSizeSlider");
            var output = document.getElementById("mapSize");
            output.innerHTML = Math.pow(2, slider.value);
            mapSize = Math.pow(2, slider.value);
            InitializeMap();
            DrawMap(mapData);
        }
    
    
    </script>

    
    <table>
        <tr>
    
            <td>
                <button onclick="InitializeMap();ResetMap();DrawMap(mapData)">NewMap</button>
                <button onclick="ResetMap()">ResetMap</button>
                <button onclick="FloodMap(mapData,false)">Flood</button>
    
                <button onclick="FloodMap(mapData,true)">JumpFlooding</button>
                <input type="range" min="1" max="7" value="7" class="slider" id="mapSizeSlider"
                    oninput="OnMapSizeChanged()">MapSize: <span id="mapSize">128</span>
                <button onclick="DrawMap(mapData)">Repaint</button>
                <slider></slider>
            </td>
        </tr>
        <tr>
            <td>You can: click "flood" or "jump flood" to see the how distance filed generated , click the pixel to check distances, the jump flooding is way faster</td>
        </tr>        
        <tr>
            <td><canvas id="canvas"></canvas></td>
        </tr>
    
    </table>
    <script>
        InitializeMap(); // init 
        var canvas = document.getElementById('canvas');
        canvas.addEventListener("mousewheel", OnCanvasMouseScroll);
        canvas.addEventListener("mousedown", OnCanvasClick);
        canvas.width = ctxScale * mapSize * gridSize;
        canvas.height = ctxScale * mapSize * gridSize;
        DrawMap(mapData)</script>
    
<script> fetch('https://fancyzero.com/pvcounter/'+encodeURIComponent(window.location.pathname), { method: 'GET' });</script>
</html>
