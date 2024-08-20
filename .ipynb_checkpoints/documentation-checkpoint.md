# Documentation for Comic Genre Classifier

### 04/24 - 05/24

- Scraping 12.09GB of images from WebToons. *CON:* Skips over scraping lots of data because of error handling (skipping) in scraping process.
- Training of initial neural network model takes ~2 hours.
- Testing accuracy: ~54% before hyperparameter tuning or optimization.

### 08/07/24

Cleaned and optimized URL and genre scraping functions.

- Commented functions and code
- Renamed variables for readability
- Turned list comprehensions iterating over the same thing into for loops
- Changed the functions to write into a dictionary instead of making two lists for keys and values that would be zipped at the end of the function

### 08/08/24

Implemented asyncio for URL and genre scraping functions

**Problem**: When requesting >56 URLs at a time, I get the error message: `IOPub data rate exceeded`. This is because I am storing the entire HTML data from each request in a list. This exceeds the memory usage of Jupyter Lab. 

**Solution 1**: Use this command `~jupyter lab --NotebookApp.iopub_data_rate_limit=1.0e10` to exceed the put in place data limit. credits to [stackoverflow](https://stackoverflow.com/questions/48256893/iopub-data-rate-exceeded-in-jupyterlab)

**Solution 2**: Change code to handle smaller portions of the URLs at a time?

---

Implemented **Solution 2**

Initial results show that, with asyncio, all 1280 requests for the comic page URLs take ~4.398s. Wihtout asyncio, this process takes ~497.3s. This signifies an improvement in efficiency by a factor of ~113.1. 

We will see if this efficiency remains after full implementation.

---

Finished implementing asyncio into the function `create_episode_url_with_genres_dict`. Scraping the episode URLs for each of the 1280 requests for the comic page URLs takes ~9.268s. Without asyncio, this process takes ~570.6s. This signifies an improvement in efficiency by a factor of ~61.6.

---

Separated comic page downloading into 2 functions:

1. Creates a dictionary mappping comic page urls to their associated genres.
2. Downloads comic page images from their urls and sorts them into directories based on their genres.

---

Implemented asyncio for comic page url and genre scraping function.

**Problem**: `TimeoutError` occured for 2,525 out of 11,352 comic page urls.

**Solution 1**: Use a try and except for comic page url scraping which gets no urls from episodes that cause `TimeoutErrors`.

**Solution 2**: Diagnose reason for error and fix function accordingly.

---

Implemented **Solution 1**

Function runs with no errors, just exceptions. comic page url dictionary contains urls for 659,264 pages from the 8,827 episodes no timeout error occurs on.

### 08/09/24

Attempted **Solution 2** by trying to diagnose the cause of the `TimeoutErrors`.

Ran the `create_page_url_with_genres_dict` function on a small subset of episode urls which had caused a timeout error. I had expected them to fail, but surprisingly they ran without issue.

---

Attempted **Solution 2** by trying to fix the cause of the `TimeoutErrors`.

Added a semaphore of 100 encompassing the requests. This had no effect on the errors.

Added a timeout parameter to the ClientSession to set the timeout limit to 2 minutes. Before setting the limit, they timed out after 5 minutes. This increased the number of `TimeoutErrors` significantly. I measured the change in errors with the number of key value pairs (comic page urls) in the dictionary resulting from the `create_page_url_with_genres_dict` function before it failed. This count went from ~659,000 to ~368,000 due to the change in timeout limit. Next, I tried timeout parameters of 1 minute and 20 seconds. The page counts for these were ~246,000 and ~97,000 respectively.

Based on the results of my timeout experimentation, setting the timeout to None appeared promising. This would permit all the requests to finish, letting the function collect data from **every** episode url. This approach proved successful, taking ~400 seconds (~6m40s) to collect 955,200 comic page urls.

---

Implemented asyncio for comic page downloading function.

Function downloads each of the 955,200 comic pgaes from the `page_url_genre_dict` and sorts them into directories based on their genre. Initial testing on 9000 comic pages each from different episodes proves promising. Runtime for a 9000 page dictionary took ~141.459 seconds. Using this ratio of pages to seconds, the downloading process of the entire `page_url_genre_dict` can be estimated to take ~4.33 hours. The process is being run overnight. 

### 08/20/24

Tested asyncio for comic page downloading function.

Function downloaded 101,559 comic pages before I stopped the kernel. The jpg images of resolutions around 800x1200 took up 26.84GB. The 955,200 comic images would have occupied too much space on my disk. Thus, I chose to terminate the program. It took 20-30 minutes to write the ~100,000 comic pages, so the speed of the function is in line with estimates.