# okhttp-post-json-image
An example in order to perform post request using okhttp to send json alongside an image.



        private const val CONNECT_TIMEOUT = 15L
        private const val READ_TIMEOUT = 15L
        private const val WRITE_TIMEOUT = 15L
        private fun performPostRequest(urlString: String, jsonString: String, image: File, token: String): String? {
            /* https://github.com/square/okhttp#requirements
            OkHttp uses your platform's built-in TLS implementation.
            On Java platforms OkHttp also supports Conscrypt,
            which integrates BoringSSL with Java.
            OkHttp will use Conscrypt if it is the first security provider: */
            Security.insertProviderAt(Conscrypt.newProvider(), 1)

            return try {
                val client = OkHttpClient.Builder()
                    .connectTimeout(CONNECT_TIMEOUT, TimeUnit.SECONDS)
                    .writeTimeout(WRITE_TIMEOUT, TimeUnit.SECONDS)
                    .readTimeout(READ_TIMEOUT, TimeUnit.SECONDS)
                    .build()

                val body: RequestBody = MultipartBody.Builder().setType(MultipartBody.FORM)
                    .addFormDataPart("parameterNameInServerSideJSON", jsonString)
                    .addFormDataPart("parameterNameInServerSideImage", "test.jpeg", image.asRequestBody("image/jpg".toMediaTypeOrNull()))
                    .build()

                val request = Request.Builder()
                    .url(URL(urlString))
                    .header("Authorization", token) // in case you use authorization
                    .post(body)
                    .build()

                val response = client.newCall(request).execute()
                response.body?.string()
            }
            catch (e: IOException) {
                e.printStackTrace()
                null
            }
        }
        
        
Implement on your gradle file
        implementation 'com.squareup.okhttp3:okhttp:4.3.1'
