
<!-- README.md is generated from README.Rmd. Please edit that file -->

# Hex colors

<!-- badges: start -->
<!-- badges: end -->

Today’s prompt is to use the following colors: #2E294E, #541388,
#F1E9DA, #FFD400, #D90368.

This is an excuse to do more truchet tiles. In particular I take this
opportunity to add an option in package {truchet} for non-random
placement of tiles in a mosaic.

Here I work with multiscale tiles using a pre-determined tile pattern as
opposed to random. Load packages:

``` r
library(ambient)
library(ggnewscale)
library(sf)
#> Linking to GEOS 3.9.1, GDAL 3.2.1, PROJ 7.2.1; sf_use_s2() is TRUE
library(tidyverse)
#> -- Attaching packages --------------------------------------- tidyverse 1.3.1 --
#> v ggplot2 3.3.5     v purrr   0.3.4
#> v tibble  3.1.6     v dplyr   1.0.7
#> v tidyr   1.1.4     v stringr 1.4.0
#> v readr   2.1.1     v forcats 0.5.1
#> -- Conflicts ------------------------------------------ tidyverse_conflicts() --
#> x dplyr::filter() masks stats::filter()
#> x dplyr::lag()    masks stats::lag()
library(truchet)
```

## Create container for mosaic

To create a data frame with placeholders for the tiles I first select
the limits and center of the mosaic, and the radius for creating a ring:

``` r
# Extent of mosaic
xlim <- c(0, 21)
ylim <- c(0, 21)

# Center of mosaic
c <- c(10, 10)

# Radius
r1 <- 3.4
r2 <- 10.4
```

Create data frame:

``` r
container <- expand.grid(x = seq(xlim[1], xlim[2], 1),
                         y = seq(ylim[1], ylim[2], 1))
```

Filter points to create a ring

``` r
container <- container %>%
  filter(sqrt((x - c[1])^2 + (y - c[2])^2) <= r2 & 
           sqrt((x - c[1])^2 + (y - c[2])^2) > r1)
```

Plot:

``` r
ggplot() + 
  geom_point(data = container,
             aes(x = x, y = y)) + coord_equal()
```

![](README_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

Mutate to add scale of tile to place at each point:

``` r
# These two variables control where the smaller tiles (scale 1/2) are used
r_scale1 <- 5
r_scale2 <- 9

container <- container %>%
  mutate(scale_p = case_when(sqrt((x - c[1])^2 + (y - c[2])^2) > r_scale2 ~ 1/2,
                             sqrt((x - c[1])^2 + (y - c[2])^2) < r_scale1 ~ 1/2,
                             TRUE ~ 1))
```

Plot:

``` r
ggplot() + 
  geom_point(data = container,
             aes(x = x, y = y, color = factor(scale_p))) + 
  coord_equal()
```

![](README_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

## Create tiles and assemble mosaic

Create truchet tiles:

``` r
t1 <- st_truchet_p(type = "f")
t2 <- st_truchet_p(type = "f", scale_p = 1/2)
```

Create mosaic:

``` r
df <- container
mosaic <- st_truchet_ms(t1 = t1, t2 = t2, df = df)
```

Create ring to intersect the mosaic in clean circles:

``` r
bf1<- data.frame(geometry = st_geometry(st_point(x = c(c[1], c[2])) %>%
                                          st_buffer(dist = 3.6))) %>% st_sf()

bf2 <- data.frame(geometry = st_geometry(st_point(x = c(c[1], c[2])) %>%
                                           st_buffer(dist = 10.5))) %>% st_sf()

bf <- st_difference(bf2, bf1)
```

## Make pretty alternative 1

Create noisy background:

``` r
perlin_noise <- long_grid(
  x = seq(xlim[1] - 1, xlim[2], length.out = 1000),
  y = seq(ylim[1] -1, ylim[2], length.out = 1000)) %>%
  mutate(
    noise = fracture(
      gen_perlin, fbm, octaves = 8, frequency = 80, x = x, y = y))
```

Set the noise gradient:

``` r
noise_gradient <- (grDevices::colorRampPalette(c("#D90368", "#FFD400")))(2)
```

Plot:

``` r
ggplot() +
  geom_raster(data = perlin_noise, aes(x, y, fill = noise)) +
  scale_fill_gradientn(colours = noise_gradient) +
  ggnewscale::new_scale_fill() +
  geom_sf(data = mosaic %>%
            st_intersection(bf),
          aes(fill = factor(color)),
          color = "#F1E9DA") +
  scale_fill_manual(values = c("#2E294E", "#541388")) +
  theme_void() +
  theme(legend.position = "none")
#> Warning: attribute variables are assumed to be spatially constant throughout all
#> geometries
```

![](README_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

``` r
# Save
ggsave("hex-colors-1.png")
#> Saving 7 x 5 in image
```

## Make pretty alternative 2:

Create noisy background:

``` r
perlin_noise <- long_grid(
  x = seq(xlim[1] - 1, xlim[2], length.out = 1000),
  y = seq(ylim[1] -1, ylim[2], length.out = 1000)) %>%
  mutate(
    noise = fracture(
      gen_perlin, fbm, octaves = 8, frequency = 80, x = x, y = y))
```

Set the noise gradient:

``` r
noise_gradient <- (grDevices::colorRampPalette(c("#2E294E", "#541388")))(2)
```

Plot:

``` r
ggplot() +
  geom_raster(data = perlin_noise, aes(x, y, fill = noise)) +
  scale_fill_gradientn(colours = noise_gradient) +
  ggnewscale::new_scale_fill() +
  geom_sf(data = mosaic %>%
            st_intersection(bf),
          aes(fill = factor(color)),
          color = "#D90368") +
  scale_fill_manual(values = c("#F1E9DA", "#FFD400")) +
  theme_void() +
  theme(legend.position = "none")
#> Warning: attribute variables are assumed to be spatially constant throughout all
#> geometries
```

![](README_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

``` r
# Save
ggsave("hex-colors-2.png")
#> Saving 7 x 5 in image
```

## Make pretty alternative 2:

Create noisy background:

``` r
perlin_noise <- long_grid(
  x = seq(xlim[1] - 1, xlim[2], length.out = 1000),
  y = seq(ylim[1] -1, ylim[2], length.out = 1000)) %>%
  mutate(
    noise = fracture(
      gen_perlin, fbm, octaves = 8, frequency = 80, x = x, y = y))
```

Set the noise gradient:

``` r
noise_gradient <- (grDevices::colorRampPalette(c("#D90368", "#FFD400")))(2)
```

Plot:

``` r
ggplot() +
  geom_raster(data = perlin_noise, aes(x, y, fill = noise)) +
  scale_fill_gradientn(colours = noise_gradient) +
  ggnewscale::new_scale_fill() +
  geom_sf(data = mosaic %>%
            st_intersection(bf),
          aes(fill = factor(color)),
          color = "#541388") +
  scale_fill_manual(values = c("#2E294E", "#F1E9DA")) +
  theme_void() +
  theme(legend.position = "none")
#> Warning: attribute variables are assumed to be spatially constant throughout all
#> geometries
```

![](README_files/figure-gfm/unnamed-chunk-20-1.png)<!-- -->

``` r
# Save
ggsave("hex-colors-3.png")
#> Saving 7 x 5 in image
```
