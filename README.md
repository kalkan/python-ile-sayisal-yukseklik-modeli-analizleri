# Python ile Mekansal Raster Görüntü İşleme Atölyesi

Kullanılan kütüphaneler: rasterio, geopandas, rasterstats

* rasterio: mekansal raster veri işlemleri için
* geopandas: mekansal veri operasyonları için
* rasterstats: vektör geometriler ile raster verilerden istatistik çıkarmak için
* contextily: internetten altık haritaları çekmek için

Bu atölyede 2 farklı notebook dosyasında Sentinel-2 ve SRTM raster verileri işlenerek bazı temel analizler gerçekleştirilecektir. 

## 1. Uygulama: SRTM Sayısal Yükseklik Verisi Okuma ve Bölgesel İstatistik

* https://search.earthdata.nasa.gov/search/granules?p=C1546314043-LPDAAC_ECS  adresinden NASADEM Merged DEM Global 1 arc second V001 veri seti araması ile sağ taraftan indireceğimiz bölgenin alanını kare içine alarak arama yapıyoruz ve ilgili bölgenin .zip uzantılı .hgt yükseklik veri setini indiriyoruz. 

* İndirdiğimiz bölgeye ait ilçe sınırlarını vektör veri formatında https://gadm.org/ sitesinden indiriyoruz. Level-2 olan veri ilçeleri içermektedir. GeoJSON formatında Level-2 veri bu çalışmada yeterli olacaktır. 
* Level-2 Türkiye GeoJSON Verisi: https://geodata.ucdavis.edu/gadm/gadm4.1/json/gadm41_TUR_2.json.zip

* İndirdiğimiz ilçe verilerini okuyup görselleştirelim.

```
# read turkey level-2 geojson
ilceler = gpd.read_file('gadm41_TUR_2.json')
ilceler.plot()
```
![image](https://user-images.githubusercontent.com/3392893/222678591-ee60052e-65e3-45e1-b73e-b04ca8222e45.png)

* İstanbul, Bursa ve Kocaeli illerini seçelim

```
secileniller = ilceler[(ilceler['NAME_1'] == 'Istanbul') | (ilceler['NAME_1'] == 'Bursa') | (ilceler['NAME_1'] == 'Kocaeli')]
```

* Bursa'nın, Karamürsel, Orhangazi ve İznik ilçelerini seçelim.

```
bursailceler = ilceler[(ilceler['NAME_2'] == 'Karamürsel') | (ilceler['NAME_2'] == 'Orhangazi') | (ilceler['NAME_2'] == 'İznik')]
```

* Seçilen ilçeleri çizdirelim. Bu ilçelere göre sayısal yükseklik modelimizi keseceğiz.

```
bursailceler.plot()
```

![image](https://user-images.githubusercontent.com/3392893/222681732-750aa630-a71e-4aa8-ad88-2ba469d29707.png)

* Şimdi sayısal yükseklik modeli verimizi öncelikle rasterio ile okuyup, rioshow ile gösterip, contextily'dan alacağımız arkaplan harita ile sunalım. Üzerine'de seçtiğimiz illerin katmanı nı ekleyelim. 

```
r = rasterio.open("n40e029.hgt")

f, ax = plt.subplots(1, figsize=(9, 9))
ax = rioshow(r, alpha=0.5, zorder=2, ax=ax)
cx.add_basemap(ax, crs=r.crs) 
secileniller.plot(facecolor="none", edgecolor="#F93822", zorder=1, ax=ax)
```

![image](https://user-images.githubusercontent.com/3392893/222686520-8bba439e-4626-44e4-a95d-b3dc19598072.png)

* Seçtiğimiz Bursa ilçeleri için görüntüyü kesmeden önce bir kere daha görselleştirelim.

```
r = rasterio.open("n40e029.hgt")

f, ax = plt.subplots(1, figsize=(9, 9))
ax = rioshow(r, alpha=0.5, zorder=2, ax=ax)
cx.add_basemap(ax, crs=r.crs) 
bursailceler.plot(facecolor="none", edgecolor="#F93822", zorder=1, ax=ax)
```
![image](https://user-images.githubusercontent.com/3392893/222706360-fb5e058b-65ca-4aca-9cd5-10d2af8b72ba.png)

* Şimdi bölgesel istatistik hesabı için rasterstats kütüphanesinden zonal_stats fonksiyonu ile ilçelerimiz için min, max, median gibi değerleri hesaplayalım. 
```
from rasterstats import zonal_stats

elevations2 = zonal_stats(bursailceler, 'n40e029.hgt',  stats="count min mean max median")
elevations2 = pandas.DataFrame(elevations2)
```
![image](https://user-images.githubusercontent.com/3392893/222713290-78ab5ce1-877a-49b9-8a89-c64e74cdf0de.png)

* Son aşamada sayısal yükseklik verisini bu ilçe sınırlarına göre keserek bir bir .tif uzantılı dosya üreteceğiz. 

* Bunun için öncelikle ilçeler verimizi geojson olarak kaydedelim.

```
bursailceler.to_file("bursa.geojson", driver="GeoJSON")
```



## Ana Başlıklar

* Raster veri okuma (SRTM)
* Bant okuma
* NDVI heasplama ve görselleştirme
* Zonal statistics
* Veri yazma 
* Vektör ile raster kesme (geojson)

