
<!-- README.md is generated from README.Rmd. Please edit that file -->

# Self-portrait

<!-- badges: start -->
<!-- badges: end -->

So I am making a self-portrait using the first few tens of thousands of
digits of pi.

I will use the following packages:

``` r
library(magick)
library(MexBrewer)
library(tidyverse)
```

## Process image

Read the image using `magick::image_read()`:

``` r
#me <- image_read("me_lg.jpg")
me <- image_read("selfie.jpg")
```

Check the size of the image:

``` r
image_info(me)
#> # A tibble: 1 x 7
#>   format width height colorspace matte filesize density
#>   <chr>  <int>  <int> <chr>      <lgl>    <int> <chr>  
#> 1 JPEG    2048   2048 sRGB       FALSE  1194725 72x72
```

Scale image:

``` r
me_scaled <- me %>% 
  image_scale("500")
```

Change the colorspace to gray:

``` r
me_scaled <- me_scaled %>% 
  image_convert(colorspace = "gray")
```

This is the image after changing to grayscale:

``` r
magick::image_ggplot(me_scaled)
```

![](README_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

Create array and number rows and columns:

``` r
img_array <- drop(as.integer(me_scaled[[1]]))
rownames(img_array) <- 1:nrow(img_array)
colnames(img_array) <- ncol(img_array):1
```

Create data frame from array and rename columns

``` r
img_df <- as.data.frame.table(img_array) %>% 
  `colnames<-`(c("y", "x", "b")) %>%
  mutate(x = as.numeric(x),
         y = - (as.numeric(y) - image_info(me_scaled)$height))
```

Sample from the image:

``` r
img_df <- img_df %>% filter(log(b) < 4.5)
```

Given the size, this means that I will use 80447 digits of pi.

## Process text

Grabbed the first one million digits of pi from here:
<https://pi2e.ch/blog/2017/03/10/pi-digits-download/#download>

``` r
text1.v <- scan("pi.txt", what="character", sep="\n")
```

Join all the lines into one long string and convert to lower case:

``` r
text1.v <- text1.v %>%
  paste(collapse = " ")
```

Extract the number of digits needed for the image:

``` r
pi.fragment <- text1.v %>%
  str_sub(1, nrow(img_df))
```

Create data frame with text:

``` r
text_df <- data.frame(img_df %>% 
                        group_by(x) %>% 
                        arrange(desc(y)),
            text = pi.fragment %>%
  str_extract_all(boundary("character")) %>% 
  unlist())
```

## Put together

Plot image with text:

``` r
col_palette <- mex.brewer("Tierra")

ggplot(text_df) +
  geom_text(aes(x = x, 
                y = y, 
                color = -b, 
                label = text,
                size = -b)) +
  coord_equal() + 
  scale_color_gradientn(colors = col_palette) +
  scale_size(range = c(0.75, 1.55)) +
  theme_void() +
  theme(legend.position = "none")
```

![](README_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

``` r
ggsave("me-pi-tierra.png", 
       width = 7,
       units = "in")
#> Saving 7 x 5 in image
```
