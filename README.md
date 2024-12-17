# Сравнение производительности алгоритма заполнения многоугольников по граничным точками и метода из библиотеки pygame


## Алгоритм заполнения многоугольника по граничным точкам

```sh
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
from matplotlib.pyplot import imshow
from PIL import Image
import numpy as np
import time
import random

def calculateX(y, P1, P2):
    try:
        k = (P2.y - P1.y)/(P2.x-P1.x)
    except ZeroDivisionError:
        return
    else:
        return (y-P1.y)/k + P1.x


def isHorizontal(P1, P2):
    return P1.y == P2.y

def getMaxMinY(points):
    maxy = points[0].y
    miny = points[0].y
    for p in points:
        if maxy < p.y:
            maxy = p.y
        if miny > p.y:
            miny = p.y
    return maxy, miny

def draw(img, y, x1, x2):
    for x in range(round(x1), round(x2)):
        img.putpixel((x, y), (0, 0, 0))

def calculate_area(points):
    n = len(points)
    area = 0.0
    for i in range(n):
        j = (i + 1) % n
        area += points[i].x * points[j].y
        area -= points[j].x * points[i].y
    area = abs(area) / 2.0
    return area

def main():
    t1 = time.time()
    img = Image.new('RGB', (800, 600), 'white')
    imshow(np.asarray(img))
    points = [Point(random.randint(0, 770), random.randint(0,570)) for _ in range(10)]
    area = calculate_area(points)
    maxy, miny = getMaxMinY(points)
    for _ in range(1000):
            for y in range(miny, maxy+1):
             border = []
             for i in range(len(points)):
                P1=points[i]
                P2=points[(i+1)%(len(points))]
                if not isHorizontal(P1, P2) and y <= max(P1.y, P2.y) and y > min(P1.y, P2.y) and P1.x != P2.x:
                     x = calculateX(y, P1, P2)
                     if x is not None:
                        border.append(x)
            border.sort()
            if len(border) >= 2:
                for pi in range(0, len(border), 1):  
                     if pi + 1 < len(border):  
                         draw(img, y, border[pi], border[pi + 1])
    
   

    t2 = time.time()
    print(f'Время выполнения: {t2 - t1}')
    print(f'Площадь многоугольника: {area}')
    
main()
```
## С использованием метода из библиотеки pygame

```sh
import pygame
import sys
import time
import random

def calculate_area(points):
    n = len(points)
    area = 0.0
    for i in range(n):
        j = (i + 1) % n
        area += points[i][0] * points[j][1]
        area -= points[j][0] * points[i][1]
    area = abs(area) / 2.0
    return area

t1 = time.time()
pygame.init()

width, height = 800, 600
screen = pygame.display.set_mode((width, height))


polygon_points = [(random.randint(0, width - 30), random.randint(0, height - 30)) for _ in range(10)]
area = calculate_area(polygon_points)
for i in range(1000):
    pygame.draw.polygon(screen, (0, 0, 0), polygon_points)
    #pygame.display.flip()

pygame.quit()
t2 = time.time()
time_e = t2 - t1
print(f"Время работы: {time_e}")
print(f"Площадь многоугольника: {area}")
sys.exit()
```
