## prepocess
* [1.tf.image.resize_images](#1)

<h2 id="1">tf.image.resize_images</h2>

[API定义](https://github.com/tensorflow/tensorflow/blob/r1.13/tensorflow/python/ops/image_ops_impl.py)

这里主要介绍preserve_aspect_ratio参数，如果这个参数设置为True，则该方法返回的图像大小会在size之内且保持原来的ratio
```python
resize_images(images,
              size,
              method=ResizeMethod.BILINEAR,
              align_corners=False,
              preserve_aspect_ratio=False)
###
preserve_aspect_ratio: Whether to preserve the aspect ratio. If this is set,
then `images` will be resized to a size that fits in `size` while
preserving the aspect ratio of the original image. Scales up the image if
`size` is bigger than the current size of the `image`. Defaults to False.
###
```

运用展示:这段代码是将图片resize到min和max之间，同时保留图片原先的比例
```python
def _resize_landscape_image(image):
  # resize a landscape image
  return tf.image.resize_images(
      image, tf.stack([min_dimension, max_dimension]), method=method,
      align_corners=align_corners, preserve_aspect_ratio=True)

def _resize_portrait_image(image):
  # resize a portrait image
  return tf.image.resize_images(
      image, tf.stack([max_dimension, min_dimension]), method=method,
      align_corners=align_corners, preserve_aspect_ratio=True)
if image.get_shape().is_fully_defined():
  if image.get_shape()[0] < image.get_shape()[1]:
    new_image = _resize_landscape_image(image)
  else:
    new_image = _resize_portrait_image(image)
  new_size = tf.constant(new_image.get_shape().as_list())
else:
  new_image = tf.cond(
      tf.less(tf.shape(image)[0], tf.shape(image)[1]),
      lambda: _resize_landscape_image(image),
      lambda: _resize_portrait_image(image))
  new_size = tf.shape(new_image)
      
```
