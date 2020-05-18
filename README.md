# draw_emnist

This repository contains code that enables users to hand-draw and classify alphabets (a-z) using deep learning. This model is trained using the EMNIST data set.


## Install Dependencies 

The following bash code will install all the necessary dependencies.

```bash
pip install -r requirements.txt
```

## Usage

If you're using the pre-trained model, then follow these steps.

```bash
git clone https://github.com/mukeshiyer237/draw_emnist.git
cd draw_emnit
jupyter notebook main.ipynb
```

Run the following cells present at the end of the notebook.

```python
from keras.models import *
import numpy as np
import cv2 

model = load_model("models/model.h5")
```

```python
classes = ['None','A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z']
```

```python
run = False
x1,y1 = -1,-1
follow = 25
img = np.zeros((450,450,1))

def predict():
    img = cv2.imread("Image.jpg")
    blur = cv2.GaussianBlur(img,(5,5),0)
    img = cv2.resize(blur, (28,28),  interpolation=cv2.INTER_CUBIC)
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

    return classes[model.predict(gray.reshape(-1,28,28,1)).argmax()]

def draw(event, x, y, flag, params):
    global run,x1,y1,img,follow
    if event == cv2.EVENT_LBUTTONDOWN:
        run = True
        ix, iy = x, y
    elif event == cv2.EVENT_MOUSEMOVE:
        if run == True:
            cv2.circle(img, (x,y), 20, (255,255,255), -1)
    
    elif event == cv2.EVENT_LBUTTONUP:
        run = False
        cv2.circle(img, (x,y), 20, (255,255,255), -1)
        gray = cv2.imwrite("Image.jpg",img)
        result = predict()
        result = 'Result : {}'.format(result)
        cv2.putText(img, org=(25,follow), fontFace=cv2.FONT_HERSHEY_SIMPLEX, fontScale=1, text= result, color=(255,0,0), thickness=1)
        follow += 25
    elif event == cv2.EVENT_RBUTTONDOWN:
        img = np.zeros((512,512,1))
        follow = 25

cv2.namedWindow('image')
cv2.setMouseCallback('image', draw)


while True:    
    cv2.imshow("image", img)
   
    if cv2.waitKey(1) == 27:
        break

cv2.destroyAllWindows()
```
