---
layout: post
title:      "Preprocessing Images with Data Split"
date:       2020-09-13 21:56:09 +0000
permalink:  preprocessing_images_with_data_split
---

https://medium.com/@andypeng93/preprocessing-images-with-data-split-77250bbfbc99

How to perform a train test split on images? While working on Flatiron School Module 4 Project — Image Classification, I came upon this issue. If you look at the data set, you will see that there are 5216 images in training folder, 16 images in validation folder and 624 images in testing folder. If you pay attention to the validation folder, it only contains 16 images which means it’s only 0.2% of the entire data set. The easy way to balance the data distribution would be to drag about 10% of our training image to the validation folder. Note, if you are using this method to remember to drag 10% of Pneumonia and 10% of Normal images from our training folder.
However, if you want to learn how to do this through coding then you have come to the right place. I will be doing this through google colaboratory and using google drive to store the files. The amount of images in our testing folder is roughly 10% of our data, therefore we won’t be touching it. First we will be running the following code.
filenames = tf.io.gfile.glob('/content/drive/My Drive/Flatiron School/chest_xray/train/*/*')
filenames.extend(tf.io.gfile.glob('/content/drive/My Drive/Flatiron School/chest_xray/val/*/*'))
train_filenames, val_filenames = train_test_split(filenames, test_size=0.2)
The function tf.io.gfile.glob returns a list that matches all the given patterns. Our variable filenames is a list that includes all the directories to all our training and validation folder. We then do a train test split on this list and assign it to train_filenames and val_filenames.
Since we cannot run our lists through the flow_from_directory function, we would need to use the flow function. The flow function takes data and label arrays and generates batches of augmented data. Therefore we would need to make some changes to our lists to create the data and label array. Below is a function that would take the argument file directory, load it as an image with size (150, 150) and then convert it to an array.
def image_arr(file):
image = tf.keras.preprocessing.image.load_img(file, target_size = (150, 150))
input_arr = tf.keras.preprocessing.image.img_to_array(image)
return input_arr
This next function would take in the argument file directory and would return 1 if ‘PNEUMONIA’ is in the directory and 0 if it is not. For example consider the directory ‘/content/drive/My Drive/Flatiron School/chest_xray/val/PNEUMONIA/person1946_bacteria_4874.jpegg’, we can see that this directory does contain the word ‘PNEUMONIA’ in it. Therefore the function will return the value 1.
def label(file):
if 'PNEUMONIA' in file:
return 1
else:
return 0
Instead of using a for loop to run through the list of directories to convert each directory into an image array, we would use the function map to apply the above functions to our list of directories train_filename and val_filename. This is because it would prevent google colaboratory from crashing by saving ram space. The map function returns a map object of the results after applying the given function to each item of the list. Therefore we would need to convert the map object to an array.
#Applying the map function along with the function above to create a map object
x_train = map(image_arr, train_filenames)
#Convert the map object to list and then numpy array aka tensor.
x_train = np.array(list(x_train))
x_val = map(image_arr, val_filenames)
x_val = np.array(list(x_val))
y_train = map(label, train_filenames)
y_train = np.array(list(y_train))
y_val = map(label, val_filenames)
y_val = np.array(list(y_val))
As a result of the above code, we would get the shape of our x_train to be (4185, 150, 150, 3). This means that there are 4185 images in our x_train with each image being a RGB (150, 150) pixels. If you have grayscale data your tensor would be (4185, 150, 150, 1), RGB data your tensor would be (4185, 150, 150, 3) and RGBA data would be (4185, 150, 150, 4). The shape of our y_train is (4185, ). This means there are 4185 values where each value is either 1 or 0 telling whether the corresponding image in x_train is an image of a patient with Pneumonia or not.
After creating the data and label arrays, we can now input them into the flow function. As you can see below, we ran x_train and y_train through train_generator. But for our test data, we will use flow_from_directory because we didn’t make changes to our test data.
train_datagen = ImageDataGenerator(1./225)
test_datagen = ImageDataGenerator(1./225)
train_generator = train_datagen.flow(x = x_train, y = y_train, batch_size = 16)
val_generator = test_datagen.flow(x = x_val, y = y_val, batch_size = 16)
test_generator = test_datagen.flow_from_directory(
'/content/drive/My Drive/Flatiron School/chest_xray/test',
target_size=(150, 150),
batch_size=16,
class_mode='binary')
Now that we have passed our data through ImageDataGenerator, we can then move on to modeling or further preprocessing techniques. For example, we can correct for imbalances because if you try to load this data the number of Pneumonia cases is three times the number of Normal cases. But if you decide to move onto modeling you can do a simple Convolution Neural Networks like below.
callback = tf.keras.callbacks.EarlyStopping(monitor='val_loss', patience=8)
model = models.Sequential()
model.add(layers.Conv2D(32, (3, 3), activation = 'relu',
input_shape = (150, 150, 3)))
model.add(layers.MaxPooling2D((2, 2)))
model.add(layers.Conv2D(64, (3, 3), activation = 'relu'))
model.add(layers.MaxPooling2D((2, 2)))
model.add(layers.Conv2D(128, (3, 3), activation = 'relu'))
model.add(layers.MaxPooling2D((2, 2)))
model.add(layers.Flatten())
model.add(layers.Dense(128, activation = 'relu'))
model.add(layers.Dense(1, activation = 'sigmoid'))
model.compile(loss = 'binary_crossentropy',
optimizer = optimizers.RMSprop(lr = 1e-4),
metrics = ['acc'])
history = model.fit_generator(train_generator,
steps_per_epoch = 261,
epochs = 30,
validation_data = val_generator,
validation_steps = 65,
verbose = 0,
callbacks = [callback])
I hope this helped you understand how to train test split images more clearly. If you have any questions, feel free to leave a comment below.
