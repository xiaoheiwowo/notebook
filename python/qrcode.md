```python
import os
import random

from PIL import Image, ImageDraw, ImageFont

width = 160
height = 50


def getRandomColor():
    r = random.randint(0, 255)
    g = random.randint(0, 255)
    b = random.randint(0, 255)
    return (r, g, b)


def getRandomChar():
    random_num = str(random.randint(0, 9))
    random_lower = chr(random.randint(97, 122))  # 小写字母a~z
    random_upper = chr(random.randint(65, 90))  # 大写字母A~Z
    random_char = random.choice([random_num, random_lower, random_upper])
    return random_char

def drawLine(draw):
    for i in range(5):
        x1 = random.randint(0, width)
        x2 = random.randint(0, width)
        y1 = random.randint(0, height)
        y2 = random.randint(0, height)
        draw.line((x1, y1, x2, y2), fill=getRandomColor())

def drawPoint(draw):
    for i in range(50):
        x = random.randint(0, width)
        y = random.randint(0, height)
        draw.point((x,y), fill=getRandomColor())


def createImg():
    bg_color = getRandomColor()
    # 创建一张随机背景色的图片
    img = Image.new(mode="RGB", size=(width, height), color=bg_color)
    # 获取图片画笔，用于描绘字
    draw = ImageDraw.Draw(img)
    # 修改字体
    path = os.path.abspath("app/util/Arial.ttf")
    print(path)
    font = ImageFont.truetype(font=path, size=36)
    text = ''
    for i in range(4):
        # 随机生成5种字符+5种颜色
        random_txt = getRandomChar()
        txt_color = getRandomColor()
        # 避免文字颜色和背景色一致重合
        while txt_color == bg_color:
            txt_color = getRandomColor()
        # 根据坐标填充文字
        draw.text((10 + 40 * i, 3), text=random_txt, fill=txt_color, font=font)
        text = text + random_txt
    # 画干扰线点
    drawLine(draw)
    drawPoint(draw)
    return img, text

  
  
# api
@api.route('/get_code_image')
def get_code_image():
    img, text = createImg()
    out = io.BytesIO()
    img.save(out, format='png')
    s = out.getvalue()
    out.close()
    resp = Response(s, mimetype="image/png")
    session['image_code'] = text
    return resp


@api.route('/judge_code')
def judge_code():
    return succeed('', session['image_code'])
```

