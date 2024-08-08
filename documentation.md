# Documentation for Comic Genre Classifier

### 04/24 - 05/24

- Scraping 12.09GB of images from WebToons. *CON:* Skips over scraping lots of data because of error handling (skipping) in scraping process.
- Training of initial neural network model takes ~2 hours.
- Testing accuracy: ~54% before hyperparameter tuning or optimization.

### 08/08/24

Cleaned and optimized URL and genre scraping functions.

- Commented functions and code
- Renamed variables for readability
- Turned list comprehensions iterating over the same thing into for loops
- Changed the functions to write into a dictionary instead of making two lists for keys and values that would be zipped at the end of the function

### 08/08/24

Implemented asyncio for URL and genre scraping functions

Problem 1: When requesting >56 URLs at a time, I get the error message: `IOPub data rate exceeded`. This is because I am storing the entire HTML data from each request in a list. This exceeds the memory usage of Jupyter Lab. 

Solution 1: Use this command `~jupyter lab --NotebookApp.iopub_data_rate_limit=1.0e10` to exceed the put in place data limit. credits to [stackoverflow](https://stackoverflow.com/questions/48256893/iopub-data-rate-exceeded-in-jupyterlab)

Solution 2: Change code to handle smaller portions of the URLs at a time?

---

Implemented solution 2

Initial results show that, with asyncio, all 1280 requests for the comic page URLs take ~4.398s. Wihtout asyncio, this process takes ~497.3s. This signifies an improvement in efficiency by a factor of ~113.1. 

We will see if this efficiency remains after full implementation.

---

Finished implementing asyncio into the function `create_episode_url_with_genres_dict`. Scraping the episode URLs for each of the 1280 requests for the comic page URLs takes ~9.268s. Without asyncio, this process takes ~570.6s. This signifies an improvement in efficiency by a factor of ~61.6.