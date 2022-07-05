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

1. Create a new Android Studio project with the *Basic Activity* template
2. Read and understand the <a href="https://dog.ceo/api/" target="_blank">Dogs REST API</a> structure and endpoints.
3. Read and understand the official documentation of the <a href="https://square.github.io/retrofit/" target="_blank">Retrofit Library</a>
3. Add the *Retrofit* library dependency to your *build.gradle (:app)* file:
   ```gradle
      //Retrofit
      implementation 'com.squareup.retrofit2:retrofit:2.9.0'
      implementation 'com.squareup.okhttp3:logging-interceptor:4.9.0'
      implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
      implementation 'com.jakewharton.retrofit:retrofit2-kotlin-coroutines-adapter:0.9.2'
      implementation 'com.squareup.picasso:picasso:2.71828'
   ```
4. Create a new package called *network* and inside define the Dtos to map the JSON structure of the Endpoints response:
   ```kotlin
      const val SHARED_PREFERENCES_FILE_NAME = "my_prefs"
      const val TOKEN_KEY= "token_key"
   ``` 
4. Implement the *Storage* interface using the *SharedPreferences*:
   ```kotlin
      class SharedPreferencesStorage(private val sharedPreferences: SharedPreferences) :Storage {

          override fun saveToken(token: String) {
              sharedPreferences.edit()
                  .putString(TOKEN_KEY, token)
                  .apply()
          }

          override fun getToken(): String? {
              return sharedPreferences.getString(TOKEN_KEY, "")
          }

          override fun clear() {
              sharedPreferences.edit().clear().apply()
          }

      }
   ```
   
### Part 2: Implement the JWT Interceptor

1. Implement the JWTInterceptor and inject the *Storage* via the constructor to retrieve the token:
   ```kotlin
      class JWTInterceptor(private val tokenStorage: TokenStorage) : Interceptor {

          override fun intercept(chain: Interceptor.Chain): Response {
              val request = chain.request().newBuilder()
              val token = tokenStorage.getToken()
              if (token != null) {
                  request.addHeader("Authorization", "Bearer $token")
              }
              return chain.proceed(request.build())
          }

      }
   ```
2. Configure the *Retrofit* instance adding the JWTInterceptor:
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

        return Retrofit.Builder()
            .baseUrl("https://dog.ceo/api/")
            .client(okHttpClient)
            .addConverterFactory(GsonConverterFactory.create(gson))
            .addCallAdapterFactory(CoroutineCallAdapterFactory())
            .build()
   ```
### Challenge Yourself: Implement a more secure Storage that encrypts the information saved.

1. Create another implementation of the *Storage* called *EncryptedStorage* that uses encryption to save the data with more security.

   ***Tip***: Find a library that helps you achieve the encryption inside the SharedPreferences or to encrypt and decrypt each data field directly.
