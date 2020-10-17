# [Eosinophils](https://en.wikipedia.org/wiki/Eosinophil) Detection

## Background

This project was set up by Data and Computer Science department, Sun Yat-sen University, and Sun Yat-sen Memorial Hospital. It aims to build a framework that could train a model to "learn" to detect any types of cells they want. And the first type they chose is the [_Eosinophils_](https://en.wikipedia.org/wiki/Eosinophil)(EOS) , which is vital in clinical testing and somewhat easy to be recognized.


Two other medical students and I were invited to try the water. The model was supposed to be used in personal computers, especially laptops. We started with three labeled slides. On each of the slides, there are about 100 - 200 _Eosinophils_. The figure below is an example.

![Our starting point](https://drive.google.com/uc?id=1J51vxy0B5uJzOhMlsfiWlqQyG0ykbsJB "Our starting point")

## Main Problems

How to train a model that can detect _Eosinophils_ in a "_reasonable correctness_" (academic study sometimes starts with an ambiguous aim)?

How to make this model run on laptops?

How to used limited resources for labeling to generate the most labels?

## Solutions I applied


![Solutions](https://drive.google.com/uc?id=1OjxGTg4-Ja8LpGvlWH40YsiZiphKg9WH "Solutions I applied")

First, I tried to use [Google's object detection API](https://github.com/tensorflow/models/tree/master/research/object_detection) in Tensorflow to train a model, which is based on faster-RCNN architecture. It failed, of course, simply because the number of images is too small. Also, I realized the model of faster-RCNN being one hundred more MB, which is quite large, and it is not possible to run on ordinary laptops fast. 

Luckily, Kaggel has hosted [a competition](https://www.kaggle.com/c/data-science-bowl-2018) about cell detections at that moment. From that competition, I met a model architecture, [U-net](https://en.wikipedia.org/wiki/U-Net). Many teams in that competition successfully used this architecture. Moreover, it's fast and relatively small, which fitted our users' needs. Still, three slides were not enough.

So the first thing was to get enough data. We decided to train a simple CNN model to help us label slides. First, we used watershed and other algorithms to segment cell-like "tiles" from the slides. Then, we used these small images to train a classification CNN model. The model worked well. And we used it to classify the cell-like objects segmented by a watershed algorithm on unlabeled slides. It's an indirect way to label slides. After that, we just corrected the wrong label, and we could get labeled slides super quickly because the CNN model has labeled many cells. The result like the below. The boxed are the model's labels, and we used green points to "correct" its labels.

![We corrected CNN's mistakes](https://drive.google.com/uc?id=188V0uw_eD-3sx6Y603XivMUstowt6w9b "We corrected CNN's mistakes")

After 40 to 50 slides, we began to use U-net as the architecture for our next model. However, U-net can only generate a possibility hot-map and needs hot-maps to train. Remember what the users asked is the count of cells, not a possibility hot map. So the U-net model cannot work alone, and we had to design a pipeline to have other components complementing the U-net model.

For training, first, we cropped each large slide into small images. Then, we used watershed algorithms to "transform" our previous label as "masks" so that U-net can be trained. When it comes to prediction, we cropped the slide for prediction into small images first; next, we used U-net to generate masks; then, masks were combined into a large mask to match the original slide. Finally, we use watershed and other algorithms to detect the region of cells. Because U-net's prediction masks will show the interest of regions as white and the background as black, it's easy for the watershed to separate the cells from this pure background. The final result and its Mask are shown below. Then, we had done it!

![Mask and its final result](https://drive.google.com/uc?id=1mFYZz77HTRM0LGBbKYvzPms9HwSTUbux)

For more details, look at in [**Here**](https://github.com/Moo-YewTsing/EOS-Detection).

## Better Solutions I know now

