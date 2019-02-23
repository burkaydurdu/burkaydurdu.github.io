---
title: Retrofit
date: "2017-05-15T22:12:03.284Z"
---

## GET
 Get methodu ile genellikle server'dan bilgi talebinde bulunuldugu zaman kullanilir.

#### @Path
```java
@GET("users/{name}/commit")
Call<List<Commit>> getCommitsByName(@Path("name") String name)
```
 Path ile url kisminda dinamik olarak değiştirebildiğimiz aralığa değerleri girmemizi sağlıyor. Bu aralık formatı ``/{değişken}/`` şeklinde olup alt satırda görülen bu methodu fonksiyon haline getiren Call ile başlayan yerde url kısmındaki değişkenlerin adini verip değer ataması yapmamızı sağlıyor.

 #### @Query
```java
@GET("users")
Call<User> getUserById(@Query("id") Integer id)
```

 Query url kısmında  **../path?** yada **.com?** yazıldıktan sonra ``name=burkay&password=1234``seklinde veriler *key=value* formatında olup birden fazla veri arasına **&** karakteri geliyor.

 ## POST
  Post metodu ile genellikle kayıt gibi işlemler yapılır. Form yapısında olan veriler server tarafına aktarılır.

#### @Field
```java
@FormUrlEncoded
@POST("/api/register")
Call<ResponseBody> registerRequest(@Field("username") String username,
                                   @Field("name") String name,
                                   @Field("surname") String surname,
                                   @Field("password") String password,
                                   @Field("email") String email);
```
Burada post metodunu etkinlestiriyoruz. Sunucu tarafinda body icinde alinacak keyler field icerigine giriliyor. Bu bilgileri request olusturdugumuz yerde veriyoruz. Burada önemli faktörlerden birisi ``@FormUrlEncoded`` Bu belirteç bize giden formatın bir form şeklinde olacağını söylüyor. Body sinde belirtilen değerler *key:value* şeklinde object formatındadır.

## PUT
Put metodu genellikle güncelleme yaparken kullanılan bir http metodudur.
Put, ``Post`` gibi field key word ile birlikte body data kısmına veriler gönderebilirsiniz. Bir diğer özelliği ise  ``get`` kullanılan path yani url uzerlinde erişim anahtarı üzerinden  veri gönderimi yapılabilir.


```java
@FormUrlEncoded
@PUT("/api/user/update/")
Call<General> userUpdate(
         @Field("user_id") String userId,
         @Field("name") String name);
```

```java
@PUT("gists/{id}")
Call<ResponseBody> updateGist(
    @Path("id") String id,
    @Body Gist gist);
```

Burada ``@Body`` olarak yazilan yapi bir class nesnesi gönderirsiniz ve o bu nesneyi çözümleyerek ilgili key-value dönüşümü yaparak veriyi iletir.

## DELETE

Delete metodu genelde veri silinmesinde  yada değiştirilmesinde kullanılan bir yapıdır. Aslında bu yapıların yaptığı işlevi Post ve Get ile hepsini gerçekleştirebiliriz. Burada kullanılan yapılar okunurluk ve düzen olarak kurulan ve kendilerine belli durumlarda yetenkler kazandırılan yapılardır. Delete nin kullanımı ile örnek verirsek.

```java
@DELETE('/delete/book')
Call<ResponseBody> deleteBook(
     @Path("id") int bookId);
```

Görüldüğü üzerine get metodunda kullanılan path yapısı, yani url üzerinden veri aktarımını sağlayan yapı ile burada bu yapının özelliğini aktardığını görmüş olduk.


## EK YAPILAR

### QueryMap
```java
@GET("/friends")
Call<ResponseBody> friends(
   @QueryMap Map<String, String> filters);
```

> Calling with foo.friends(ImmutableMap.of("group", "coworker", "age", "42")) yields /friends?group=coworker&age=42.

Bu query map ile java tarafından query kodunu hash map fonksiyonu ile setleyip yollayabilmemize yarıyor.

```java
@GET("/news")
Call<List<News>> getNews(
        @Query("page") int page,
        @Query("order") String order,
        @Query("author") String author,
        @Query("published_at") Date date,
        …
);
```

Bu şekilde tek tek girmek herine yukarıda ki yapıyı kullanabiliriz veriyi gönderirken


```java
Map<String, String> data = new HashMap<>();
   data.put("author", "Marcus");
   data.put("page", String.valueOf(2));
```

HashMap bu şekilde setleyip gönderebiliriz.


### FieldMap

```java
@FormUrlEncoded
@PUT("user")
Call<User> update(
  @FieldMap Map<String, String> fields);
```

Görüldüğü gibi post veya put yapılarında kullanılan field nesnesi body içinde key : value olarak yapıları setler. Bu şekilde tek tek girmek yerine Bu verileri Map haline getiririz. QueryMap yaptığımız gibi HashMap fonksiyonu ile setler nesnesini yapıya göndeririz.

```java
@FormUrlEncoded
@PUT("user")
Call<User> update(
         @Field("username") String username,
         @Field("name") String name,
         @Field("email") String email,
         @Field("homepage") String homepage,
         @Field("location") String location
);
```

Bu şekilde göndermek yerine yukarıdaki gibi gönderebiliyoruz.


### Headers
```java
@Headers({
     "Accept: application/vnd.yourapi.v1.full+json",
     "User-Agent: Your-App-Name"
})
@GET("/tasks/{task_id}")
Task getTask(@Path("task_id") long taskId);
```

Headerlar gönderilen verileri betimleyen anlaşılması sağlayan başlık bilgileridir. Burada gönderilen dosyanın formatı, dil ayarları ve birçok bilgi burada setlenerek gönderilir karşı taraf ise bu dosyayı alınca önce header'ı okur.

Header dinamik olarak güncellenebilir. Burada aşağıdaki örneği inceleyelim.

```java
@GET("/tasks")
 Call<List<Task>> getTasks(
   @Header("Content-Range") String contentRange);
```

### HeaderMap
```java
@GET("/tasks")
Call<List<Task>> getTasks(
      @HeaderMap Map<String, String> headers
);
```

FieldMap yada QueryMap gibi çokca içerik varsa hashmap fonksiyonu ile daha toplu ve hızlı sonuca oluşmamızı sağlıyor.

### FormUrlEncoded
```java
@FormUrlEncoded
@POST("/api/user/reset/password")...
```
Post veya Put gibi metotları formUrl formatına dönüştürüp gönderilmesini sağlayan yapıdır.

### Retrofit Buildir Ve Çalıştırılması

Gerekli Metotlar tanımlanıp kullanılması tarafına geçilince öncelikle Retrofit nesnesini oluşturulması gereklidir.

```java
private ApiAutService service;
...
private void onCreateService() {
       Retrofit loginApi = new Retrofit.Builder()
               .baseUrl(Service.BASE_URL)
               .addConverterFactory(GsonConverterFactory.create())
               .build();
       service = loginApi.create(ApiAutService.class);
}
```

Yukarıda oluşturulan yapı. Retrofit nesnesi oluşturuyoruz. Başlangıç url adresini veriyoruz. Daha sonra ``GsonConverterFactory`` ile metotlarda kullanılan yapılarda gson ile json gelen formatlar class lara dönüştürülmesini sağlıyor. Daha sonra bu oluşturulan retrofit nesnesi ile birlikte ilgili interface objesi setleniyor. Elimizde artık yazdığımız metotları kullanabildiğimiz bir nesne bulunuyor.

```java
Call<LoginData> loginSocialRequest = service.loginSocialRequest(id, platform.toString());
    loginSocialRequest.enqueue(new retrofit2.Callback<LoginData>() {
        @Override
        public void onResponse(@NonNull Call<LoginData> call, @NonNull Response<LoginData> response) {
            if (response.body() != null) {
                if (response.body().getSuccess()) {
                    ..
                } else {
                    ..
                }
            } else { message(R.string.server_response_error, true); }
        }
        @Override
        public void onFailure(@NonNull Call<LoginData> call, @NonNull Throwable t) {
            message(R.string.server_response_error, true);
            Log.e("Serve error", t.getMessage());
       }
});
```

Yukarıda görüldüğü üzere yazılan metotları çağırıp asenkron çalışan callback metodu oluşturulur. Daha sonra verilen objeye göre gson dönüşümü yapılır, daha sonra veriler gerekli class formatına dönüştürülünce  ``response`` parametresinde bulunan ``response.body()`` içinde gerekli class nesnesi görülür. Bir çok yapıda kullandığımız için gelen veri tek bir tane değilde liste şeklinde geliyorsa. Metot oluşturulurken ``Call<List<ObjeClass>>`` diye verilerek gelen veri bir liste şeklinde olur. Daha sonra bu liste yukarıdaki yapı ile alınarak gerekli **ArrayList** nesnesine setlenerek kullanılabilir.
