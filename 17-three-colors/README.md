
<!-- README.md is generated from README.Rmd. Please edit that file -->

# Three colors

<!-- badges: start -->
<!-- badges: end -->

I will experiment with some outlines given by selective hatching.

``` r
library(MexBrewer)
library(sf)
library(tidyverse)
```

## Create some Truchet tiles

Create a one-by-one tile:

``` r
tile <- matrix(c(0, 0, 
                 0, 1, 
                 1, 1,  
                 1, 0,
                 0, 0),
               ncol = 2,
               byrow = TRUE)

# Convert coordinates to polygons and then to simple features
tile <- data.frame(id = 1,
                   r = NA,
                   geometry = st_polygon(list(tile)) %>% 
                     st_sfc()) %>% 
  st_as_sf()
```

Plot tile:

``` r
ggplot() +
  geom_sf(data =  tile)
```

![](README_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

Now create two points:

``` r
pts <- data.frame(id = c("points_1", "points_2", "points_1", "points_2"), 
                  x = c(0, 0, 1, 1),
                  y = c(0, 1, 1, 0))

# Convert coordinates to points and then to simple features
pts <- pts %>%
  st_as_sf(coords = c("x", "y"))
```

Create circle segments:

``` r
cs <- c(0.5)

bfs <- pts %>% 
  mutate(r = cs[1], 
         geometry = pts %>%
           st_buffer(dist = r) %>% 
           pull(geometry)) %>% 
  st_cast(to = "LINESTRING")
#> Warning in st_cast.sf(., to = "LINESTRING"): repeating attributes for all sub-
#> geometries for which they may not be constant
```

``` r
bfs_1 <- bfs %>%
  filter(id == "points_1") %>%
  st_intersection(st_geometry(tile))
#> Warning: attribute variables are assumed to be spatially constant throughout all
#> geometries

bfs_2 <- bfs %>%
  filter(id == "points_2") %>%
  st_intersection(st_geometry(tile))
#> Warning: attribute variables are assumed to be spatially constant throughout all
#> geometries
```

Make tiles:

``` r
tile_1 <- bfs_1

tile_2 <- bfs_2
```

Translate so that the origin is at the center of the tile:

``` r
tile_1 <- tile_1 %>%
  mutate(geometry = st_geometry(tile_1) + c(-0.5, -0.5))

tile_2 <- tile_2 %>%
  mutate(geometry = st_geometry(tile_2) + c(-0.5, -0.5))

tiles <- rbind(data.frame(tile_1, tile = 1),
               data.frame(tile_2, tile = 2)) %>% 
  st_as_sf()
```

Function for Truchet tiles:

``` r
st_truchet <- function(t, xlim = c(1, 8), ylim = c(1, 12)){
  # Create grid for placing tiles
  df <- data.frame(expand.grid(x = seq(xlim[1], xlim[2], 1),
                               y = seq(ylim[1], ylim[2], 1)))
  
  # Create container for mosaic
  container <- matrix(c(min(df$x) - 0.5, min(df$y) - 0.5, 
                        min(df$x) - 0.5, max(df$y) + 0.5, 
                        max(df$x) + 0.5, max(df$y) + 0.5,  
                        max(df$x) + 0.5, min(df$y) - 0.5,
                        min(df$x) - 0.5, min(df$y) - 0.5),
                      ncol = 2,
                      byrow = TRUE)
  
  # Convert coordinates of container to polygons and then to simple features
  container <- data.frame(id = 1,
                          r = NA,
                          geometry = st_polygon(list(container)) %>% 
                            st_sfc()) %>% 
    st_as_sf()
  
  mosaic <- data.frame()
  
  for(i in 1:nrow(df)){
    mosaic <- rbind(mosaic,
                    t %>%
                      filter(tile == sample.int(2, 1)) %>%
                      mutate(group = i,
                             geometry = geometry + c(df[i, 1], df[i, 2])) %>%
                      st_as_sf())
  }
  return(list(container = container, mosaic = mosaic))
}
```

``` r
mosaic <- st_truchet(tiles)
container <- mosaic[["container"]]
mosaic <- mosaic[["mosaic"]]
```

Union:

``` r
mosaic_buffer <- st_union(mosaic)
```

``` r
mosaic_buffer <- rbind(data.frame(group = 1, 
                           st_buffer(mosaic_buffer, dist = c(0.2))) %>% 
                  st_as_sf(),
                data.frame(group = 2, 
                           st_buffer(mosaic_buffer, dist = c(0.1))) %>% 
                  st_as_sf(),
                data.frame(group = 3, 
                           st_buffer(mosaic_buffer, dist = c(0.05))) %>% 
                  st_as_sf())
```

``` r
col_palette <- mex.brewer("Atentado")
col_palette_1 <- col_palette[c(5, 6)]
col_pal1ette_2 <- col_palette[-c(5, 6)]
#col_palette_2 <- col_palette_2[sample.int(8, 3)] 

# Plot
ggplot() +
  geom_sf(data = container) +
  geom_sf(data = mosaic_buffer,
          aes(fill = factor(group)))
```

![](README_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

Create hatching patterns:

``` r
# Number of points for hatching pattern
n_hatch <- 7500

# Create vertical hatching pattern
df_hatch_1 <- data.frame(x = runif(n_hatch, 
                                 min = st_bbox(container)[1], 
                                 max = st_bbox(container)[3]),
                       y = runif(n_hatch,
                                 min = st_bbox(container)[2],
                                 max = st_bbox(container)[4])) %>%
  # Calculate endpoints for the line segments that will produce the hatching
  mutate(xend = x,
         yend = y - runif(n(), 
                          min = 0.5, 
                          max = runif(n(), 
                                      min = 0.5,
                                      max = y/3 + 0.5)))
df_hatch_1_coords <- df_hatch_1 %>% 
  select(x, 
         y)

# Create horizontal hatching pattern
df_hatch_2 <- data.frame(x = runif(n_hatch, 
                                 min = st_bbox(container)[1], 
                                 max = st_bbox(container)[3]),
                       y = runif(n_hatch,
                                 min = st_bbox(container)[2],
                                 max = st_bbox(container)[4])) %>%
  # Calculate endpoints for the line segments that will produce the hatching
  mutate(xend = x + runif(n(), 
                         min = 0.5, 
                         max = runif(n(), 
                                     min = 0.5,
                                     max = y/4 + 0.5)),
         yend = y)

df_hatch_2_coords <- df_hatch_2 %>% 
  select(x, 
         y)

# Create diagonal hatching pattern
df_hatch_3 <- data.frame(x = runif(n_hatch, 
                                 min = st_bbox(container)[1], 
                                 max = st_bbox(container)[3]),
                       y = runif(n_hatch,
                                 min = st_bbox(container)[2],
                                 max = st_bbox(container)[4])) %>%
  # Calculate endpoints for the line segments that will produce the hatching
  mutate(l = runif(n(), 
                         min = 0.5, 
                         max = runif(n(), 
                                     min = 0.5,
                                     max = y/4 + 0.5)),
         xend = x + l,
         yend = y - l)

df_hatch_3_coords <- df_hatch_3 %>% 
  select(x, 
         y)
```

Plot these hatching patterns:

``` r
ggplot() +
  geom_segment(data = df_hatch_1 %>%
                 filter(xend > 1 & xend < 8, 
                        yend > 1 & yend < 12),
               aes(x = x, 
                   y = y,
                   xend = xend,
                   yend = yend),
                   #alpha = (y/30)^4),
               size = 0.1) +
  geom_segment(data = df_hatch_2 %>%
                 filter(xend > 1 & xend < 8, 
                        yend > 1 & yend < 12),
               aes(x = x, 
                   y = y,
                   xend = xend,
                   yend = yend),
                   #alpha = (y/30)^4),
               size = 0.1) +
  geom_segment(data = df_hatch_3 %>%
                 filter(xend > 1 & xend < 8, 
                        yend > 1 & yend < 12),
               aes(x = x, 
                   y = y,
                   xend = xend,
                   yend = yend),
                   #alpha = (y/30)^4),
               size = 0.1) +
  coord_equal()
```

![](README_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

Make a function to convert hatching to sf:

``` r
make_line <- function(x, y, xend, yend) {
    st_linestring(matrix(c(x, xend, y, yend), 2, 2))
}
```

Convert to sf:

``` r
df_hatch_1 <- df_hatch_1 %>%
    select(x, y, xend, yend) %>% 
    pmap(make_line) %>%
  st_as_sfc()

df_hatch_2 <- df_hatch_2 %>%
    select(x, y, xend, yend) %>% 
    pmap(make_line) %>%
  st_as_sfc()

df_hatch_3 <- df_hatch_3 %>%
    select(x, y, xend, yend) %>% 
    pmap(make_line) %>%
  st_as_sfc()
```

Crop the hatching patterns using the container:

``` r
df_hatch_1 <- df_hatch_1_coords %>% 
  mutate(geometry = st_geometry(df_hatch_1)) %>%
  st_as_sf() %>%
  st_crop(container)
#> Warning: attribute variables are assumed to be spatially constant throughout all
#> geometries

df_hatch_2 <- df_hatch_1_coords %>% 
  mutate(geometry = st_geometry(df_hatch_2)) %>%
  st_as_sf() %>%
  st_crop(container)
#> Warning: attribute variables are assumed to be spatially constant throughout all
#> geometries

df_hatch_3 <- df_hatch_3_coords %>% 
  mutate(geometry = st_geometry(df_hatch_3)) %>%
  st_as_sf() %>%
  st_crop(container)
#> Warning: attribute variables are assumed to be spatially constant throughout all
#> geometries
```

Plot:

``` r
ggplot() +
  geom_sf(data = df_hatch_1) +
  geom_sf(data = df_hatch_2) +
  geom_sf(data = df_hatch_3) +
  geom_sf(data = container,
          color = "blue",
          fill = NA)
```

![](README_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->

Use the truchet tiles to crop pattern 2:

``` r
df_hatch_1_cropped <- df_hatch_1 %>%
  st_intersection(mosaic_buffer %>%
                    filter(group == 1))
#> Warning: attribute variables are assumed to be spatially constant throughout all
#> geometries

df_hatch_2_cropped <- df_hatch_2 %>%
  st_difference(mosaic_buffer %>%
                    filter(group == 1))
#> Warning: attribute variables are assumed to be spatially constant throughout all
#> geometries

df_hatch_3_cropped <- df_hatch_3 %>%
  st_difference(mosaic_buffer %>%
                    filter(group == 1))
#> Warning: attribute variables are assumed to be spatially constant throughout all
#> geometries
```

Plot the cropped pattern:

``` r
ggplot() +
  geom_sf(data = df_hatch_1_cropped,
          aes(alpha = y)) +
  geom_sf(data = df_hatch_2_cropped,
          aes(alpha = y)) +
  geom_sf(data = df_hatch_3,
          aes(alpha = y)) +
  geom_sf(data = container,
          color = "blue",
          fill = NA)
```

![](README_files/figure-gfm/unnamed-chunk-21-1.png)<!-- -->

Make pretty.

### Frida

``` r
col_palette <- mex.brewer("Frida")[sample.int(10, 3)]

ggplot()  +
  geom_sf(data = df_hatch_3_cropped,
          aes(alpha = ((st_bbox(container)[3] - x) * (st_bbox(container)[4] - y))),
          color = col_palette[1],
          size = 0.2) +
  geom_sf(data = df_hatch_1_cropped,
          aes(alpha = x * y),
          color = col_palette[2],
          size = 0.2) +
  geom_sf(data = df_hatch_2_cropped,
          aes(alpha = ((st_bbox(container)[3] - x) * (st_bbox(container)[4] - y))),
          color = col_palette[3],
          size = 0.2) +
  theme_void() +
  theme(legend.position = "none")
```

![](README_files/figure-gfm/unnamed-chunk-22-1.png)<!-- -->

``` r
ggsave(filename = "three-colors-frida.png", 
       width = 4, 
       height = 6, 
       units = "in")
```

### Aurora

``` r
col_palette <- mex.brewer("Aurora")[sample.int(10, 3)]

ggplot()  +
  geom_sf(data = df_hatch_3_cropped,
          aes(alpha = ((st_bbox(container)[3] - x) * (st_bbox(container)[4] - y))),
          color = col_palette[1],
          size = 0.2) +
  geom_sf(data = df_hatch_1_cropped,
          aes(alpha = x * y),
          color = col_palette[2],
          size = 0.2) +
  geom_sf(data = df_hatch_2_cropped,
          aes(alpha = ((st_bbox(container)[3] - x) * (st_bbox(container)[4] - y))),
          color = col_palette[3],
          size = 0.2) +
  theme_void() +
  theme(legend.position = "none")
```

![](README_files/figure-gfm/unnamed-chunk-23-1.png)<!-- -->

``` r
ggsave(filename = "three-colors-aurora.png", 
       width = 4, 
       height = 6, 
       units = "in")
```

### Atentado

``` r
col_palette <- mex.brewer("Atentado")[sample.int(10, 3)]

ggplot()  +
  geom_sf(data = df_hatch_3_cropped,
          aes(alpha = ((st_bbox(container)[3] - x) * (st_bbox(container)[4] - y))),
          color = col_palette[1],
          size = 0.2) +
  geom_sf(data = df_hatch_1_cropped,
          aes(alpha = x * y),
          color = col_palette[2],
          size = 0.2) +
  geom_sf(data = df_hatch_2_cropped,
          aes(alpha = ((st_bbox(container)[3] - x) * (st_bbox(container)[4] - y))),
          color = col_palette[3],
          size = 0.2) +
  theme_void() +
  theme(legend.position = "none")
```

![](README_files/figure-gfm/unnamed-chunk-24-1.png)<!-- -->

``` r
ggsave(filename = "three-colors-atentado.png", 
       width = 4, 
       height = 6, 
       units = "in")
```

### Revolucion

``` r
col_palette <- mex.brewer("Revolucion")[sample.int(10, 3)]

ggplot()  +
  geom_sf(data = df_hatch_3_cropped,
          aes(alpha = ((st_bbox(container)[3] - x) * (st_bbox(container)[4] - y))),
          color = col_palette[1],
          size = 0.2) +
  geom_sf(data = df_hatch_1_cropped,
          aes(alpha = x * y),
          color = col_palette[2],
          size = 0.2) +
  geom_sf(data = df_hatch_2_cropped,
          aes(alpha = ((st_bbox(container)[3] - x) * (st_bbox(container)[4] - y))),
          color = col_palette[3],
          size = 0.2) +
  theme_void() +
  theme(legend.position = "none")
```

![](README_files/figure-gfm/unnamed-chunk-25-1.png)<!-- -->

``` r
ggsave(filename = "three-colors-revolucion.png", 
       width = 4, 
       height = 6, 
       units = "in")
```
