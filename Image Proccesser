# -*- coding: utf-8 -*-
"""
Created on Tue May 24 20:33:18 2022

@author: Samet

Pyhton code for transforming an image into a suitible format for the printer to print.

"""


from PIL import Image
import numpy as np  


im = Image.open("pencilsketchadjusted-4402546.jpeg")
hght = int(198*im.height/im.width)
im = im.resize((198,hght)).convert('L')
data = np.asarray(im)


#Creates the configuration file.
config = open('config.txt','w')
config.write((str(im.height).zfill(3))+str(im.width).zfill(3)+'00')
config.close()


img = open('image.txt','w')
for i in data:
    for j in i:
        if j > 128:
            img.write("0")
        else: 
            img.write("1")
img.close()  


line = 0
img1 = open('image1.txt','w')
while line < hght/4:
    for j in data[line]:
        if (256-j) < 128:
            img1.write("0")
        else: 
            img1.write("1")
    line += 1
img1.close() 


config1 = open('config1.txt','w')
config1.write(str(line).zfill(3)+'198'+'03')
config1.close()
line_prev = line



img2 = open('image2.txt','w')
while line < hght/2:
    for j in data[line]:
        if (256-j) < 128:
            img2.write("0")
        else: 
            img2.write("1")
        
    line += 1
img2.close() 

config2 = open('config2.txt','w')
config2.write(str(line-line_prev).zfill(3)+'198'+'02')
config2.close()
line_prev = line


img3 = open('image3.txt','w')

while line < 3*hght/4:
    for j in data[line]:
        if (256-j) < 128:
            img3.write("0")
        else: 
            img3.write("1")
    line += 1
img3.close() 

config3 = open('config3.txt','w')
config3.write(str(line-line_prev).zfill(3)+'198'+'01')
config3.close()
line_prev = line


img4 = open('image4.txt','w')


while line < hght:
    for j in data[line]:
        if (256-j) < 128:
            img4.write("0")
        else: 
            img4.write("1")
        
    line += 1
img4.close()       

config4 = open('config4.txt','w')
config4.write(str(line-line_prev).zfill(3)+'198'+'00')
config4.close()
line_prev = line


im.show()
    



