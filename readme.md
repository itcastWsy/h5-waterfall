# 瀑布流

## 什么是瀑布流

又称为瀑布流布局，是一种比较经典的网站布局方式，尤其多见于图片较多的页面。常见有两种瀑布流方式。分别为

1. 定宽不定高
2. 定高不定宽

### 定宽不定高

![2022-11-29001026](assets/2022-11-29001026.png)

### 定高不定宽

![image-20221129001124812](assets/image-20221129001124812.png)

## 定宽不定高-思路

1. 动态生成 1000 个 li 标签，设置统一的宽度 设置透明度 （实际开发中，只需要获取 1000 个图片对应的高度即可）
2. 开启定时器，每一行只放 3 个 li 标签 ，三个 li 标签，按照正常顺序依次摆放即可，后面的 li 标签切换逻辑
3. 每显示一个 li 标签，就需要自己维护好一个数组 [第 1 列的 li 标签高度集合，第 2 列的 li 标签高度集合，第 3 列的 li 标签高度集合]
4. 统计出那一列 最低 和 对应的下标(0,1,2)
5. 将下一个 li 标签 放在对应下标的位置，同时计算出下标对应列的高度总和，设置为 即将显示的 li 标签的 top

## 代码

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
      }
      li {
        width: 33.33%;
        border: 1px solid #000;
        font-size: 30px;
        text-align: center;
        position: absolute;
        top: 0;
        left: 0;
        list-style: none;
      }
    </style>
  </head>
  <body>
    <ul></ul>
    <script>
      init();
      function init() {
        const ul = document.querySelector("ul");
        for (let index = 0; index < 1000; index++) {
          const li = document.createElement("li");
          li.innerText = index;
          li.style.backgroundColor = getRandomColor();
          li.style.height =
            Math.floor(Math.random() * (200 - 50 + 1) + 50) + "px";
          li.style.opacity = "0";
          ul.appendChild(li);
        }

        function getRandomColor() {
          const color1 = Math.floor(Math.random() * 256);
          const color2 = Math.floor(Math.random() * 256);
          const color3 = Math.floor(Math.random() * 256);
          return `rgb(${color1},${color2},${color3})`;
        }

        setWaterFall();
        function setWaterFall() {
          const lis = [...document.querySelectorAll("li")];
          let index = 0;
          const heightList = [[], [], []];
          const timeId = setInterval(() => {
            const li = lis.shift();
            if (!li) {
              clearInterval(timeId);
              return;
            }
            if (index < 3) {
              const left = index * li.clientWidth + "px";
              li.style.left = left;
              heightList[index].push(li.clientHeight);
            } else {
              // 计算出3个列分别对应的高度 [1,2,3]
              const sumHeightList = heightList.reduce(
                (sumColumnHeight, columnList) => [
                  ...sumColumnHeight,
                  columnList.reduce((a, b) => a + b, 0),
                ],
                []
              );
              const { minIndex, minHeight } = getMinIndex(sumHeightList);
              const left = minIndex * li.clientWidth + "px";
              const top = minHeight + "px";
              li.style.left = left;
              li.style.top = top;
              heightList[minIndex].push(li.clientHeight);
            }
            li.style.opacity = "1";
            index++;
          }, 500);
        }

        function getMinIndex(arr) {
          let minIndex = 0;
          let minHeight = arr[0];
          for (let index = 0; index < arr.length; index++) {
            const element = arr[index];
            if (minHeight > element) {
              minHeight = element;
              minIndex = index;
            }
          }
          return { minIndex, minHeight };
        }
      }
    </script>
  </body>
</html>
```

## 效果

![二〇二二年十一月二十九日](assets/%E4%BA%8C%E3%80%87%E4%BA%8C%E4%BA%8C%E5%B9%B4%E5%8D%81%E4%B8%80%E6%9C%88%E4%BA%8C%E5%8D%81%E4%B9%9D%E6%97%A5.gif)
