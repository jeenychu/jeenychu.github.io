```python
# !pip install Pillow`
# !pip install opencv-python # Use opencv for finding outlines of the differences in the pictures
# !pip install pyautogui # automate the mouse click
```


```python
import pyautogui
from PIL import ImageChops # for screenshot
import cv2
import os, time
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
```


```python
# The coordiate of original image
# upper left: (0, 23)

# The coordinate of the comparing image
# upper left: (964, 23)

# Image Size
# width 955, height 762

while True:
    result = pyautogui.confirm('hidden catch!', buttons=['start', 'end'])
    if result == 'end':
        break # end the program
    
    width, height = 956, 762
    y_pos = 23

    src = pyautogui.screenshot(region=(0, y_pos, width, height))
    src.save('scr.jpg')

    dest = pyautogui.screenshot(region=(964, y_pos, width, height))
    dest.save('dest.jpg')

    # compare two images pixel by pixel -> detect the difference in RGB
    diff = ImageChops.difference(src, dest)
    diff.save('diff.jpg')

    # draw the outlines for the differences 
    # I need to click the middle point of the outline boxes.

    while not os.path.exists('diff.jpg'):
        time.sleep(1) # create a loop that waits until a file named 'diff.jpg' is created in the current directory.


    # src_img = cv2.imread('src.jpg')
    # dest_img = cv2.imread('dest.jpg')
    diff_img = cv2.imread('diff.jpg')
    gray = cv2.cvtColor(diff_img, cv2.COLOR_BGR2GRAY) # make gray image. cvtColor = convert color. In opencv, images are dealt as BGR, not RBG.
    gray = (gray > 25) * gray
    contours, _ = cv2.findContours(gray, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_NONE)   # The method findCoutours returns two objects, and I will contours only.
    # cv2.RETR_EXTERNAL -> Out of all the outlinese created, retrieve the external one
    # cv2.CHAIN_APPROX_NONE: All the boundary points are stored, resulting in a more complex contour representation.
    # cv2.CHAIN_APPROX_SIMPLE: Only the end points of each contour are stored, resulting in a simpler contour representation with fewer points.
    COLOR = (0, 200, 0) # BGR-> give values to Green only -> ends up giving a green color
    for cnt in contours:
        if cv2.contourArea(cnt) > 100: # The area of contour has to be bigger than 100
            x, y, width, height = cv2.boundingRect(cnt)  # Used to find the bounding rectangle that completely encloses the contour.
            # cv2.rectangle(src_img, (x, y), (x+width, y+height), COLOR, 2) # 왼쪽 위 좌표, 오른쪽 아래 좌표 알려줌
            # cv2.rectangle(dest_img, (x, y), (x+width, y+height), COLOR, 2)
            cv2.rectangle(diff_img, (x, y), (x+width, y+height), COLOR, 2)

            to_x = x + (width // 2) 
            to_y = y + (height // 2) + y_pos # y_pos should be added because of the little 
            pyautogui.moveTo(to_x, to_y, duration=0.15)
            pyautogui.click(to_x, to_y)
            # time.sleep(1)

       # cv2.imshow('src', src_img)
       # cv2.imshow('dest', dest_img)
       cv2.imshow('diff', diff_img)
       # cv2.waitkey(0) # closes all the popped windows when pressed random key
       # cv2.destroyAllWindows()
```


```python

```
