<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>自定义转盘</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            padding: 20px;
        }
        #wheelCanvas {
            margin: 0 auto;
            display: block;
        }
        #optionsInput {
            width: 80%;
            height: 100px;
        }
        #spinButton {
            margin: 20px;
            padding: 10px 20px;
            font-size: 18px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <h1>自定义转盘</h1>
    <textarea id="optionsInput" placeholder="请输入转盘选项，用逗号分隔，例如：苹果,香蕉,橙子"></textarea><br>
    <button id="updateButton">更新转盘</button>
    <canvas id="wheelCanvas" width="500" height="500"></canvas><br>
    <button id="spinButton">旋转转盘</button>
    <h2 id="result"></h2>

    <script>
        const canvas = document.getElementById("wheelCanvas");
        const ctx = canvas.getContext("2d");
        const resultText = document.getElementById("result");
        const spinButton = document.getElementById("spinButton");
        const updateButton = document.getElementById("updateButton");
        const optionsInput = document.getElementById("optionsInput");

        let options = ["苹果", "香蕉", "橙子", "草莓", "葡萄", "西瓜"];
        let startAngle = 0;
        let spinning = false;

        function drawWheel() {
            const arc = (2 * Math.PI) / options.length;
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            options.forEach((option, index) => {
                const angle = startAngle + index * arc;
                ctx.beginPath();
                ctx.moveTo(canvas.width / 2, canvas.height / 2);
                ctx.arc(
                    canvas.width / 2,
                    canvas.height / 2,
                    canvas.width / 2,
                    angle,
                    angle + arc
                );
                ctx.fillStyle = index % 2 === 0 ? "#FFD700" : "#FFA500"; // 交替颜色
                ctx.fill();
                ctx.stroke();

                // 绘制文字
                ctx.save();
                ctx.translate(
                    canvas.width / 2 + Math.cos(angle + arc / 2) * (canvas.width / 2.5),
                    canvas.height / 2 + Math.sin(angle + arc / 2) * (canvas.height / 2.5)
                );
                ctx.rotate(angle + arc / 2);
                ctx.fillStyle = "black";
                ctx.font = "16px Arial";
                ctx.fillText(option, -ctx.measureText(option).width / 2, 0);
                ctx.restore();
            });
        }

        function spinWheel() {
            if (spinning) return;
            spinning = true;
            let spinAngle = Math.random() * 2000 + 2000; // 随机旋转角度
            let spinDuration = 3000; // 持续时间
            let startTime = null;

            function animate(time) {
                if (!startTime) startTime = time;
                const progress = Math.min((time - startTime) / spinDuration, 1);
                const ease = progress < 0.5 ? 2 * progress * progress : -1 + (4 - 2 * progress) * progress;

                startAngle += ease * spinAngle;
                drawWheel();

                if (progress < 1) {
                    requestAnimationFrame(animate);
                } else {
                    spinning = false;
                    const arc = (2 * Math.PI) / options.length;
                    const index = Math.floor(
                        ((2 * Math.PI - (startAngle % (2 * Math.PI))) % (2 * Math.PI)) / arc
                    );
                    resultText.textContent = `结果：${options[index]}`;
                }
            }

            requestAnimationFrame(animate);
        }

        function updateWheel() {
            const input = optionsInput.value.trim();
            if (input) {
                options = input.split(",").map(opt => opt.trim());
                drawWheel();
                resultText.textContent = "";
            } else {
                alert("请输入至少一个选项！");
            }
        }

        // 初始绘制
        drawWheel();

        // 事件绑定
        spinButton.addEventListener("click", spinWheel);
        updateButton.addEventListener("click", updateWheel);
    </script>
</body>
</html>
