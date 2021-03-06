Basilica provides 4 functions for working with images:

-   `embed_image`
-   `embed_images`
-   `embed_image_file`
-   `embed_image_files`

The `embed_image_file` and `embed_image_files` functions take a
`characther` vector (a string) with a file path pointing to an image. On
the other hand, `embed_image` and `embed_images` take a `raw` vector
obtained through `readBin`.

#### `embed_image_file`

``` {.r}
download.file("https://www.basilica.ai/static/images/tutorial/dog-test-1.jpg", "/tmp/dog1.jpg")
download.file("https://www.basilica.ai/static/images/tutorial/dog-test-2.jpg", "/tmp/dog2.jpg")
download.file("https://www.basilica.ai/static/images/tutorial/cat-test-1.jpg", "/tmp/cat.jpg")

library('basilica')
conn <- connect("SLOW_DEMO_KEY")

embeddings = list()
embeddings[[1]] = embed_image_file("/tmp/dog1.jpg", conn=conn)
embeddings[[2]] = embed_image_file("/tmp/dog2.jpg", conn=conn)
embeddings[[3]] = embed_image_file("/tmp/cat.jpg", conn=conn)

print(cor(embeddings[[1]], embeddings[[2]]))
print(cor(embeddings[[1]], embeddings[[3]]))
```

#### `embed_image_files`

``` {.r}
download.file("https://www.basilica.ai/static/images/tutorial/dog-test-1.jpg", "/tmp/dog1.jpg")
download.file("https://www.basilica.ai/static/images/tutorial/dog-test-2.jpg", "/tmp/dog2.jpg")
download.file("https://www.basilica.ai/static/images/tutorial/cat-test-1.jpg", "/tmp/cat.jpg")


library('basilica')
conn <- connect("SLOW_DEMO_KEY")

embeddings = embed_image_files(c("/tmp/dog1.jpg", "/tmp/dog2.jpg", "/tmp/cat.jpg"), conn=conn)

print(cor(embeddings[1,], embeddings[2,]))
print(cor(embeddings[1,], embeddings[3,]))
```

#### `embed_image`

``` {.r}
download.file("https://www.basilica.ai/static/images/tutorial/dog-test-1.jpg", "/tmp/dog1.jpg")
download.file("https://www.basilica.ai/static/images/tutorial/dog-test-2.jpg", "/tmp/dog2.jpg")
download.file("https://www.basilica.ai/static/images/tutorial/cat-test-1.jpg", "/tmp/cat.jpg")

library('basilica')
conn <- connect("SLOW_DEMO_KEY")

embeddings = list()

f <- file("/tmp/dog1.jpg", "rb")
dog1_raw <- readBin(f, "raw", file.info("/tmp/dog1.jpg")[1, "size"])
close(f)
embeddings[[1]] = embed_image(dog1_raw, conn=conn)

f <- file("/tmp/dog2.jpg", "rb")
dog2_raw <- readBin(f, "raw", file.info("/tmp/dog2.jpg")[1, "size"])
close(f)
embeddings[[2]] = embed_image(dog2_raw, conn=conn)

f <- file("/tmp/cat.jpg", "rb")
cat_raw <- readBin(f, "raw", file.info("/tmp/cat.jpg")[1, "size"])
close(f)
embeddings[[3]] = embed_image(cat_raw, conn=conn)

print(cor(embeddings[[1]], embeddings[[2]]))
print(cor(embeddings[[1]], embeddings[[3]]))
```

#### `embed_images`

``` {.r}
download.file("https://www.basilica.ai/static/images/tutorial/dog-test-1.jpg", "/tmp/dog1.jpg")
download.file("https://www.basilica.ai/static/images/tutorial/dog-test-2.jpg", "/tmp/dog2.jpg")
download.file("https://www.basilica.ai/static/images/tutorial/cat-test-1.jpg", "/tmp/cat.jpg")

library('basilica')
conn <- connect("SLOW_DEMO_KEY")

embeddings = list()

f <- file("/tmp/dog1.jpg", "rb")
dog1_raw <- readBin(f, "raw", file.info("/tmp/dog1.jpg")[1, "size"])
close(f)

f <- file("/tmp/dog2.jpg", "rb")
dog2_raw <- readBin(f, "raw", file.info("/tmp/dog2.jpg")[1, "size"])
close(f)

f <- file("/tmp/cat.jpg", "rb")
cat_raw <- readBin(f, "raw", file.info("/tmp/cat.jpg")[1, "size"])
close(f)

embeddings = embed_images(list(dog1_raw, dog2_raw, cat_raw), conn=conn)

print(cor(embeddings[1,], embeddings[2,]))
print(cor(embeddings[1,], embeddings[3,]))
```
