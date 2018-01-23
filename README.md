# Data Visualization for AfSIS I Tanzania
    # W. Simbila, January 2018
    
    install.packages(c("downloader","rgdal","raster","rgeos","maptools", "leaflet", "htmlwidgets"), dependencies=T)
    suppressPackageStartupMessages({
      require(rgeos)
      require(downloader)
      require(rgdal)
      require(raster)
      require(sp)
      require(maptools)
      require(leaflet)
      require(htmlwidgets)
    })
    
    # Data setup --------------------------------------------------------------
    # create a data folder in your current working directory
    setwd("~")
    dir.create("AfSIS_I", showWarnings=F)
    setwd("./AfSIS_I")
    
    # download GADM-L3 shapefile (courtesy: http://www.gadm.org)
    download("https://www.dropbox.com/s/sp18lokhw5vzgoh/Regions.zip?dl=1", "Regions.zip", mode="wb")
    unzip("Regions.zip", overwrite=T)
    shape <- shapefile("Regions.shp")
    plot(shape)
    
    # read field dataset for TZ 
    tz <- read.csv("Tz_field_details.csv")
    
    # converting point data into point shapefiles
    require(raster)
    
    projection(tz) = "+init=espg:4326" # WGS84 coords
    
    tzshape <- shapefile(tz, "tz.shp")
    
    # Load both two shapefiles -------------------------
    
    # read 
    orstations_path <- "/Users/bartlein/Projects/ESSD/data/shp_files/OR/"
    orstations_name <- "orstations.shp"
    orstations_file <- paste(orstations_path, orstations_name, sep="")
    orstations_shp <- readShapePoints(orstations_file)
    
    
    # extract ROI coordinates
    coord <- coordinates(tz)
    index <- extract(tz.points, coord)
    index <- as.data.frame(cbind(coord, index))
    rmask <- index[which(index$index == 1),]
    
  
    
    # Sampling map widget -----------------------------------------------------
    # render map
    w <- leaflet() %>% 
      addProviderTiles(providers$OpenStreetMap.Mapnik) %>%
      addCircleMarkers(sloc$x, sloc$y, clusterOptions = markerClusterOptions())
    w ## plot widget 
    
    # save widget
    saveWidget(w, 'TZ_sample.html', selfcontained = T)
