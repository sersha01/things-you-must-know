# Compress image while uploading

I created a model of vendor who have 2 image fields. User might upload high quality images which result in slow app loading time to render all these image. so we can use a compressed image to load in bulk which reduces the rendering time and we can use the real image when viewing the entire vendor page

# Prerequisites
### Make sure you installed
	1. Pillow
	2. Pypng

# Code

models.py
```python
from datetime import datetime
from PIL import Image


def get_random_string():
    chars = string.ascii_lowercase
    strin = ''.join(random.choice(chars) for _ in range(6))
    date = datetime.now().strftime("%m%d%H%M%S")
    return  date + strin


def image_compressor(imagefile, dir_name):
    image_location_head = settings.MEDIA_ROOT
    image_location = image_location_head + "/" + imagefile
    foo = Image.open(image_location)
    width, height = foo.size

    if height >= 250 and width >= 250:
        if height == width:
            foo = foo.resize((250, 250), Image.ANTIALIAS)
        elif height > width:
            aspect_ratio = height / width
            new_width = 250
            new_height = new_width * aspect_ratio
            foo = foo.resize((int(new_width), int(new_height)), Image.ANTIALIAS)
        else:
            aspect_ratio = width / height
            new_height = 250
            new_width = new_height * aspect_ratio
            foo = foo.resize((int(new_width), int(new_height)), Image.ANTIALIAS)

    filename = "cf" + str(get_random_string()) + '.png'
    save_location = image_location_head + "/" + dir_name + "/" + filename
    foo.save(save_location, optimize = True, quality = 100)
    return '/media/' + dir_name + "/" + filename


class Vendor(models.Model):

    def get_file_path(instance, filename):
        ext = filename.split('.')[-1]
        tmp = get_random_string()
        filename = "%s.%s" % (tmp, ext)
        return os.path.join('vendor', filename)

    ...
    logo = models.ImageField(upload_to = get_file_path, null = True, blank = True,
                             default = '../static/image-404/photo-unavailable.png')
    thumbnail_logo = models.CharField(max_length = 255, null = True, blank = True, default = None)
    image = models.ImageField(upload_to = get_file_path, null = True, blank = True,
                              default = '../static/image-404/photo-unavailable.png')
    thumbnail_image = models.CharField(max_length = 255, null = True, blank = True, default = None)
	...

    __original_image = None
    __original_logo = None

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.__original_image = self.image.url
        self.__original_logo = self.logo.url

    def save(self, *args, **kwargs):
        super(Vendor, self).save(*args, **kwargs)
        if self.image.url != self.__original_image:
            image_path = (self.image.url.split('/media/')[-1])
            thumbnail_image_path = image_compressor(image_path, "vendor")
            self.thumbnail_image = thumbnail_image_path
            self.__original_image = thumbnail_image_path
            super(Vendor, self).save(*args, **kwargs)
        if self.logo.url != self.__original_logo:
            image_path = (self.logo.url.split('/media/')[-1])
            thumbnail_logo_path = image_compressor(image_path, "vendor")
            self.thumbnail_logo = thumbnail_logo_path
            self.__original_logo = thumbnail_logo_path
            super(Vendor, self).save(*args, **kwargs)

    def __str__(self):
        return self.title

```

### So what this code do?
Basically while any of the image either it be creating new entry or updateing the current field, it will check if the value of images has changed.
If changed it will compress the image using Pillow and store to a dirextory
