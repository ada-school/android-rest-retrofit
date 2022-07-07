<img align="right" src="https://github.com/ada-school/module-template/blob/main/ada.png">


## Consuming a REST API from Android with Retrofit and Coroutines

Implement the code required to consume REST API Endpoints using Retrofit and Coroutines.

**Learning Objectives**

- Explain how the threads system works on Android.
- User coroutines to 
- Implement an Interceptor to a JWT to the request using Retrofit.


**Main Topics**

* REST.
* Coroutines.
* Retrofit.
* Service.



## Codelab 🧪

🗣️ "I hear and I forget I see and I remember I do and I understand." Confucius



### Part 1: Use Retrofit to consume REST API Endpoints

1. Create a new Android Studio project with the *Basic Activity* template.
2. Read and understand the <a href="https://dog.ceo/api/" target="_blank">Dogs REST API</a> endpoints and the structure of the response.
3. Read and understand the official documentation of the <a href="https://square.github.io/retrofit/" target="_blank">Retrofit Library</a>
3. Add the *Retrofit* library dependenciens to your *build.gradle (:app)* file:
   ```gradle
      //Retrofit
      implementation 'com.squareup.retrofit2:retrofit:2.9.0'
      implementation 'com.squareup.okhttp3:logging-interceptor:4.9.0'
      implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
      implementation 'com.jakewharton.retrofit:retrofit2-kotlin-coroutines-adapter:0.9.2'
      implementation 'com.squareup.picasso:picasso:2.71828'
   ```
4. Create a new package called *network* and inside create the following Data Transfer Objets(DTOs) to map the JSON structure of the endpoints response data:
   ```kotlin
        
         data class RandomDogImageDto(val message: String, val status: String)
         
         data class DogsListDto(val message: Map<String, List<String>>, val status: String)
   ``` 
4. Create a new interface inside the *network* package called *DogsImageService* to map the structure and HTTP methods use to consume the different endpoints:
   ```kotlin
      interface DogsImageService {

          @GET("breeds/image/random")
          suspend fun getRandomDogImage(): Response<RandomDogImageDto>

          @GET("breeds/list/all")
          suspend fun getDogBreedsList(): Response<DogsListDto>

      }
   ```
5. Configure and instantiate the *Retrofit* class to be able to instantiate the *DogsImageService* interface:
    ```kotlin
           val loggingInterceptor = HttpLoggingInterceptor()
              loggingInterceptor.level = HttpLoggingInterceptor.Level.BODY
           val okHttpClient = OkHttpClient.Builder()
               .addInterceptor(loggingInterceptor)
               .addInterceptor(jwtInterceptor)
               .writeTimeout(0, TimeUnit.MILLISECONDS)
               .readTimeout(2, TimeUnit.MINUTES)
               .connectTimeout(1, TimeUnit.MINUTES).build()

           val gson = GsonBuilder()
               .setDateFormat("yyyy-MM-dd'T'HH:mm:ssX")
               .create()

           val retrofit = Retrofit.Builder()
               .baseUrl("https://dog.ceo/api/")
               .client(okHttpClient)
               .addConverterFactory(GsonConverterFactory.create(gson))
               .addCallAdapterFactory(CoroutineCallAdapterFactory())
               .build()
           val dogImageService = retrofit.create(DogsImageService::class.java)
      ``` 
6. Inside the *onCreate* function on the *MainActivity* add the code to instantiate the *dogImageService* and test your implementation creating and launching a coroutine:
   ```kotlin
         GlobalScope.launch(Dispatchers.IO) {
               val response = dogsImageService.getRandomDogImage()
               if (response.isSuccessful) {
                   val randomDogImageDto = response.body()
                   Log.d("Developer", "Response:  $randomDogImageDto")                   
               }
           }
   ```

### Challenge Yourself: Implement a service to retrieve the image of any Dog Breed

1. Implement another function in the *DogsImageService* that lets you retrieve the picture of any given dog breed.

   ***Tip***: Search for URL MANIPULATION in the Retrofit Official Documentation Page.
