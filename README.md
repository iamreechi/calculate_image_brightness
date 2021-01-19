# find-if-an-image-is-dark-or-overexpose-and-move-to-another-folder
## Idea 
Certain regions of images will be selected to pickup the brightness level and finally averaging those value will give the average brightness level of the image.

>Finally after getting an average brightness level value we can decide a threshold
>below which images will be considered as dark
>one more interesting this is, the code is highly dynamic
>meaning we can modify it to pick only certain percent of points from the image
>for finding averagae brightness values.
>No matter whether the image size is 100X100 pixels or 1000X1000 pixels
>the program is unaffacted by image size.


## External Modules required
```
>> pip install opencv-contrib-python
>> pip install numpy
>> pip install imutils
```


>BTW you can place your own images inside the images directory or
>modify the code a little bit to input your own images
>just change the path to you local machine path where the images are in line 31
>put all images in a folder called images in your local machine 
>and change line 32 to the path where your dark images should be copied to  are and line 33 to the path where over bright images should be copied to  
>Images that are nither over bright or dark will remain in images folder

### Actual image High Resolution
![Original image](https://github.com/iamreechi/calculate_image_brightness/blob/master/images/low_size_bright.jpg)

### Actual image Low Resolution
![Original image](https://github.com/iamreechi/calculate_image_brightness/blob/master/images/low_size_dark.jpg)

### Here is a little code snippet
>to help you understand whats going on behind the scenes

*to run the script you must be in the directory that has your images and type this in your CLI python over_bright.py --images*
```
#------------------------Reading image--------------------------------------
for imagePath in paths.list_images('images'):
	#-----Reading the image-----------------------------------------------------
	img = cv2.imread(imagePath)
	img = imutils.resize(img, width=900)
	img_dot = img

	#-----Converting image to LAB Color model----------------------------------- 
	lab= cv2.cvtColor(img, cv2.COLOR_BGR2LAB)

	#-----Splitting the LAB image to different channels-------------------------
	l, a, b = cv2.split(lab)

	#-----Finding average lightness level in image by fixing some points--------
	y,x,z = img.shape #height, width of image
	print('>> Image Dimension => X:{}, Y:{}'.format(x,y))
	#Now we will decide some dynamic points on image for checking light intensity
	l_blur = cv2.GaussianBlur(l, (11, 11), 5)
	maxval = []
	count_percent = 3 #percent of total image
	count_percent = count_percent/100
	row_percent = int(count_percent*x) #1% of total pixels widthwise
	column_percent = int(count_percent*y) #1% of total pixel height wise


	folder_path = ('C:/Users/RICHIE/Desktop/computer_Vision_Project/calculate_image_brightnes/')
	dest = "C:/Users/RICHIE/Desktop/computer_Vision_Project/calculate_image_brightnes/dark_images/"
	dest2 = "C:/Users/RICHIE/Desktop/computer_Vision_Project/calculate_image_brightnes/expose_verybright_img/"

	for i in range(1,x-1):
		if i%row_percent == 0:
			for j in range(1, y-1):
				if j%column_percent == 0:
					pix_cord = (i,j)
					
					#cv2.circle(img_dot, (int(i), int(j)), 5, (0, 255, 0), 2)
					img_segment = l_blur[i:i+3, j:j+3]
					(minVal, maxVal, minLoc, maxLoc) = cv2.minMaxLoc(img_segment)
					maxval.append(maxVal)

	avg_maxval = round(sum(maxval) / len(maxval))
	print('>> Total points: {}'.format(len(maxval)))
	print('>> Average Brightness: {}'.format(avg_maxval))


	if avg_maxval<50:
		print('>> Image is Dark')
		text = 'Dark'
		shutil.move(folder_path + imagePath, dest)

	elif avg_maxval>200:
		print('>> Image is Over_exposed')
		text = 'Over_exposed'
		shutil.move(folder_path + imagePath, dest2)

	else:
		print('>> Image is Brightness is ok')
		text = 'ok'
```
>Firstly we created a for loop to loop through our folder of images
>we converted the image into L*A*B color spaces
>did a slight Gaussian Blurring to cancel out the Noises.
>then splitting it to extract the ``L`` Channel which is responsible for the brightness levels
>after which we calculate number of points we are going to check
>rowise and column wise
>in our case we are taking every 1% pizel out of total pizels
>and finally we detect how dark or overexpose level at that point
>in the end we just average out all the values

Hope you enjoyed today's tutorial. Don't forget to star the repository.
if you have any query, feel free to ask.


Connect me at ``https://www.linkedin.com/in/richie-uchenna-akparuorji-85180111b/``


