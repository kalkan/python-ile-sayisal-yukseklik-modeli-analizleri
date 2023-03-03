# Python ile Mekansal Raster Görüntü İşleme Atölyesi

Kullanılan kütüphaneler: rasterio, geopandas, rasterstats

* rasterio: mekansal raster veri işlemleri için
* geopandas: mekansal veri operasyonları için
* rasterstats: vektör geometriler ile raster verilerden istatistik çıkarmak için

Bu atölyede 2 farklı notebook dosyasında Sentinel-2 ve SRTM raster verileri işlenerek bazı temel analizler gerçekleştirilecektir. 

## 1. Uygulama: SRTM Sayısal Yükseklik Verisi Okuma ve Bölgesel İstatistik

* https://search.earthdata.nasa.gov/search/granules?p=C1546314043-LPDAAC_ECS  adresinden NASADEM Merged DEM Global 1 arc second V001 veri seti araması ile sağ taraftan indireceğimiz bölgenin alanını kare içine alarak arama yapıyoruz ve ilgili bölgenin .zip uzantılı .hgt yükseklik veri setini indiriyoruz. 

* İndirdiğimiz bölgeye ait ilçe sınırlarını vektör veri formatında https://gadm.org/ sitesinden indiriyoruz. Level-2 olan veri ilçeleri içermektedir. GeoJSON formatında Level-2 veri bu çalışmada yeterli olacaktır. 
* Level-2 Türkiye GeoJSON Verisi: https://geodata.ucdavis.edu/gadm/gadm4.1/json/gadm41_TUR_2.json.zip

'''
# read turkey level-2 geojson
ilceler = gpd.read_file('gadm41_TUR_2.json')
ilceler.plot()
'''

## Ana Başlıklar

* Raster veri okuma (SRTM)
* Bant okuma
* NDVI heasplama ve görselleştirme
* Zonal statistics
* Veri yazma 
* Vektör ile raster kesme (geojson)

