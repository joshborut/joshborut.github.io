---
layout: post
title: There's no I in API (Lab 2)
subtitle: Tackling APIs with the help of Ms. Feng
cover-img: /assets/img/API_cover.png
thumbnail-img: /assets/img/API_thumbnail.png
share-img: /assets/img/API_cover.png
tags: [labs]
author: Joshua Borut
---

You put in your order and wait eagerly for your food, only to find that the waiter brings back a *JSON*! As it turns out, you accidentally requested from an __API__, which works much like a restaurant. In this lab, we explore API usage by querying ~The World Bank API~ (fancy!). Without further ado, let's get (js)on with the lab!

# Part 1

## 1. *Write a `CountryYear` class*

### Method
This portion of the assignment was quite straightforward; all I had to do was apply the basics of object oriented programming. This left me with the following:

```python
class CountryYear:
    def __init__(self, name, country_iso3, year, life_expectancy, gdp_per_capita, population, cereal_yield,
                 access_to_electricity, literacy_rate, inflation, undernourishment):
        self.name = name
        self.country_iso3 = country_iso3
        self.year = year
        self.life_expectancy = life_expectancy
        self.gdp_per_capita = gdp_per_capita
        self.population = population
        self.cereal_yield = cereal_yield
        self.access_to_electricity = access_to_electricity
        self.literacy_rate = literacy_rate
        self.inflation = inflation
        self.undernourishment = undernourishment
```

## 2. *Write a method get_world_bank_data(indicator) that queries the World Bank Indicators API*

### Method

As per usual when querying an API, I began by importing the requests library. Then, I used a formatted string literal to create the `api_url` concisely. I faced an issue where calling the endpoint did not provide all of the accessible data because the database is paginated with a default `per_page` value of 50. After playing around with the `per_page` query parameter, I found that the maximum entries is 32767. I was curious why this is the case so I googled the significance of the number and found that it is 2^15-1, which is apparently the maximum 16-bit signed integer--pretty cool ([Source](https://stackoverflow.com/questions/30653212/what-is-the-significance-of-the-number-32767))! After fixing this issue, I knew that I would get the maximum data coverage (hooray!). Once I called the API using the requests module, I proceeded to check the status code of the API to ensure that the data existed. If it did not, I returned `None`. Lastly, I simply turned the data into a JSON file and returned the result of the query, excluding the page formatting information which I circumvented using `data[1]` rather than simply `data`.

```python
def get_world_bank_data(indicator):
    # Per page
    api_url = f"https://api.worldbank.org/v2/country/all/indicator/{indicator}?format=json&per_page={2**15-1}"
    # Querying API
    api_call = requests.get(api_url)
    # Checking if the query is valid
    if api_call.status_code == 200:
        # converting api call into json
        data = api_call.json()
        return data[1]
    else:
        return None
```

I chose  __cereal yield__ (id: `AG.YLD.CREL.KG`), __access to electricity (% of population)__ (id: `EG.ELC.ACCS.ZS`), __literacy rate of people 15-24__ (id: `SE.ADT.1524.LT.ZS`), __annual consumer price inflation__ (id: `FP.CPI.TOTL.ZG`), and __prevalence of undernourishment__ (id: `SN.ITK.DEFC.ZS`). I chose these five indicators because they give a general sense of a country's agriculture, economy, health, and infrastructure.

## 3. *Write a method that, based on all the world bank data, returns a list of `CountryYear` objects that has all your chosen indicators filled in*

### Method
Here is where things started to get a bit confusing. With the help of Ms. Feng, I realized that I'd have to use nested dictionaries to generate the list. I started by creating a function `get_indicator_data_dict(indicators)` that returns a nested dictionary with all the necessary data to create `CountryYear` objects. 

This function takes a list of indicators to query and begins by using list comprehension to store all of the different JSON data. Then, it loops through the JSONs, each of which corresponds to a specific type of indicator data. Within each JSON, it loops through each country-year entry and stores the relevant data in variables. Then, it uses the `get()` method to ensure that the relevant keys in the nested dictionary are populated such that an entry can be made. Once those checks are complete, it adds all of the country-year data stored in the aforementioned variables to the dictionary. 

```python
def get_indicator_data_dict(indicators):
    country_year_dict = {}

    # Creating list of JSONs from indicator data
    indicator_JSONs = [get_world_bank_data(indicator) for indicator in indicators]
    for indicator_JSON in indicator_JSONs:
        for country_data in indicator_JSON:
            # Saving the relevant data
            iso3_code = country_data['countryiso3code']
            country_name = country_data['country']['value']
            year = country_data['date']
            indicator = country_data['indicator']['id']
            indicator_value = country_data['value']

            # Ensuring that the relevant nested dictionaries exist
            country_year_dict[year] = country_year_dict.get(year, {})
            country_year_dict[year][iso3_code] = country_year_dict[year].get(iso3_code, {})

            # Adding the data to the nested dictionary
            country_year_dict[year][iso3_code]['name'] = country_name
            country_year_dict[year][iso3_code][indicator] = indicator_value
    return country_year_dict
```

The dictionary output of this function made writing `get_country_year_list()` far easier. This function simply iterates through all of the country-year pairs provided by the previously created function and appends the relevant data to the `CountryYear` list before returning the complete list.

```python
def get_country_year_list():
    country_year_list = []
    # Specifying the indicators
    indicators = ['SP.DYN.LE00.IN', 'NY.GDP.PCAP.CD', 'SP.POP.TOTL', 'AG.YLD.CREL.KG', 'EG.ELC.ACCS.ZS', 'SE.ADT.1524.LT.ZS', 'FP.CPI.TOTL.ZG', 'SN.ITK.DEFC.ZS']
    # Getting all the indicator data
    indicator_data_dict = get_indicator_data_dict(indicators)
    # Iterating through the country-year pairs
    for year in indicator_data_dict.keys():
        if int(year) < 1993:
            for iso_code3 in indicator_data_dict[year].keys():
                # TODO: Filter countries (TBD!)
                # Getting the data for a specific country-year pair
                country_data = indicator_data_dict[year][iso_code3]
                # Adding CountryYear object to list
                country_year_list.append(CountryYear(country_data['name'], iso_code3, year, country_data['SP.DYN.LE00.IN'], country_data['NY.GDP.PCAP.CD'], country_data['SP.POP.TOTL'], country_data['AG.YLD.CREL.KG'], country_data['EG.ELC.ACCS.ZS'], country_data['SE.ADT.1524.LT.ZS'], country_data['FP.CPI.TOTL.ZG'], country_data['SN.ITK.DEFC.ZS']))
    return country_year_list
    return country_year_list
```

### Failures (to learn from!)

Creating this function took a lot of trial and error. I initially tried to get every country iso3 code in a function `get_countries`, but that approach failed for two main reasons: (1) the World Bank Data API's [endpoint for all countries](https://api.worldbank.org/v2/country/all/) actually only has iso2 codes, which feels like quite an oversight, and (2) indicator data has regions listed in addition to countries, meaning that iterating only through countries would not account for all of the accessible data. My second approach was utilizing the multi-indicator functionality offered by the API, which you accomplish with a semicolon (e.g. AG.YLD.CREL.KG;EG.ELC.ACCS.ZS). I also considering using the `data` query parameter, but ultimately chose against it because it was easier to filter years in my own code. Admittedly, this may be slightly slower because it requires requesting more data, but I still found it easier. 

### Filtering

I chose not to filter the data much in terms of countries because I wanted to look globally. I may decide to focus on just one country per continent if I find looking at all of them too difficult, which should not be too difficult to implement in part 2 (I could simply add if statements checking if the data is from the desired countries or alter `api_url` in `get_world_bank_data` to use iso3 codes in a manner like `chn;bra` instead of `all`). However, I did exclude years after 1993 because I figured it would be interesting to analyze the indicators before the spread of the internet in 1993 ([Source](https://www.npr.org/2023/04/30/1172276538/world-wide-web-internet-anniversary#:~:text=Trump's%20Terms-,The%20World%20Wide%20Web%20became%20available%20to%20the%20broader%20public,with%20graphics%2C%20audio%20and%20hyperlinks.)).

## 4. *Print results from queries into a CSV*

### Method

I decided to utilize the `DictWriter` module because it would make writing to a file easier in my `create_country_years_csv(country_year_list)` function, which takes the output of the `get_indicator_data_dict(indicators)` method. I began by providing `DictWriter` the required headers and target file fields. Then, I wrote the header to the file and iterated through the provided `country_year_list` to write each entry to the CSV. I searched online and found that all classes have a `__dict__` attribute that returns a dictionary with the argument-value pairs of a specific object. This made using `writer.writerow()` far simpler.

```python
def create_country_years_csv(country_year_list):
    # Reference source for using DictWriter: https://docs.python.org/3/library/csv.html
    with open('country_years.csv', 'w', newline='') as f:
        fieldnames = ['name', 'country_iso3', 'year', 'life_expectancy', 'gdp_per_capita', 'population', 'cereal_yield', 'access_to_electricity', 'literacy_rate', 'inflation', 'undernourishment']
        writer = DictWriter(f, fieldnames=fieldnames)
        writer.writeheader()
        for country_year in country_year_list:
            # Reference source for using __dict__: https://stackoverflow.com/questions/61517/python-dictionary-from-an-objects-fields
            writer.writerow(country_year.__dict__)
```

## Bringing it all together!

Now, it was just a matter of calling the functions and creating the CSV. I felt like being fancy so I even added a `__main__` check!

```python
if __name__ == '__main__':
    country_year_list = get_country_year_list()
    create_country_years_csv(country_year_list)
```

### Takeaways

Although frustrating at some points, this lab was a good way to practice my API usage skills. I think I improved in terms of understanding documentation and using nested-dictionaries. I look forward to part 2! Thanks for all of the help Ms. Feng :)

# Part 2

We are back with *Part 2*! Now, it's time to use Ms. Feng's API. I knew that I would be using her server a lot, so I started by defining the base URL.

```python
MS_FENG_SERVER_BASE_URL = "https://ifenghm.pythonanywhere.com"
```

## *Endpoint 0*

### Method

The next step to use Ms. Feng's API was to create an API key. I achieved this by creating the function `create_api_key` that takes the user's first and last names. The function uses a formatted string with the inputs to call `requests.post()` with the necessary `json` and `header` information. Then, it changes the response to a JSON from which it accesses the `apikey` entry. I wanted to store my API key securely, so I added the API key to a __.env__ file. The code for this looks like the following:

```python
def create_api_key(first_name, last_name):
    # Prepare request info
    username = f"{first_name.lower()}_{last_name.lower()}"
    headers = {"Content-Type": "application/json"}
    json = {"username": username}
    api_url = MS_FENG_SERVER_BASE_URL + "create_api_key"

    # Create post request
    response = requests.post(api_url, headers=headers, json=json)

    # Access the api key from the response
    response_json = response.json()
    api_key = response_json["apikey"]

    print(f"Your API key is {api_key}")

    # Write API Key to env file
    with open(".env", "w") as f:
        f.write(f"API_KEY={api_key}")
```

Because I was using a __.env file__, I knew that I would have to access `API_KEY` at some point. So, I used the `python-dotenv` module to securely access the __.env__. I had to research online a bit, but I eventually got the hang of it ([Source](https://dev.to/emma_donery/python-dotenv-keep-your-secrets-safe-4ocn)).

```python
def get_api_key():
    # Load the env file
    dotenv.load_dotenv()
    # Access the key from the env file
    api_key = os.getenv("API_KEY")
    if api_key is None:
        # Raise an error if the key does not exist
        raise Exception("Key does not exist. Use create_api_key() to generate one.")
    else:
        return api_key
```

### Problems

For whatever reason, my code gave me an error after the first time I ran it. I thought that it might be because the API would only recognize `joshua_borut` as a user instead of `josh_borut`, so I called `create_api_key()` again with the altered username. To my disappointment, I received another error. So, I checked with CURL to see what the problem was, finding that the API keys had been generated; it was simply some issue with `response.json()`. After resolving the error, I had to instead use `josh_borut1`to get my API key. Then, I could move to the next section!

## *Endpoint 2*

### Method

Before I could pass any information to Ms. Feng's API, I had reevaluate my data filtering. I wanted to continue with my original plan of filtering out all data after the creation of the internet, but I realized that there was no data available for the *prevalence of undernourishment before 1983*. So, I removed my previous filtering and decided to continue on.

Now that I had to simply arrange my indicators in a nested dictionary, I created a function `country_year_list_to_indicator_dict()` (definitely a mouthful but I prefer descriptive names!) that takes in a `country_year_list`. This function simply appends to a dictionary where the keys are indicators with empty lists as their values. I accomplished this by using iterating through the list of country year objects and using `__dict__` again (it turned out to be a super helpful discovery!) to append the relevant data. 

```python
def country_year_list_to_indicator_dict(country_year_list):
    # Create the desired dictionary
    indicator_dict = {"cereal_yield": [], "access_to_electricity": [], "literacy_rate": [], "inflation": [],
            "undernourishment": []}
    
    for country_year in country_year_list:
        # Access the properties of the country year object
        country_year_props = country_year.__dict__
        for prop in indicator_dict.keys():
            prop_value = country_year_props[prop]
            # Add the relevant properties to the dictionary
            indicator_dict[prop].append(prop_value)
    return indicator_dict
```

Next, I used this function to create a post request with Ms. Feng's API that gets the file ID for my desired indicator dictionary. This was pretty straightforward; I just had to use my API key and pass the necessary JSON data. This JSON came from the previously defined `country_year_list_to_indicator_dict()` function.

```python
def get_file_id(country_year_list):
    api_url = MS_FENG_SERVER_BASE_URL + "/upload"
    api_key = get_api_key()
    json = country_year_list_to_indicator_dict(country_year_list)
    headers = {"apikey": api_key}

    # Create the post request
    response = requests.post(api_url, headers=headers, json=json)

    # Access the data
    response_json = response.json()
    id = response_json['id']

    return id
```

### Struggles

This section was not too bad! My approach was pretty direct, and I didn't encounter any issues to be honest! The one change I made was an alteration to my `get_file_id()` function. I used to have `api_key` as a parameter, but I realized that my API key never changed because it was stored in the __.env__. So, I chose to use `get_api_key()` directly in my `get_file_id()` function.

## *Endpoint 2*

### Method
This last function did not take much to complete. All I had to do was made a basic API call using the query parameters of `iv`, `dv`, and `file_id`. Rather than return `p_value` I decided to print it because I simply want to observe it. This left me with the following function:

```python
def analyze_data(iv, dv, file_id):
    api_url = MS_FENG_SERVER_BASE_URL + "/analyze"
    params = {
        "independent": iv,
        "dependent": dv,
        "id": file_id}
    # Create the API call
    response = requests.get(api_url, params=params)

    # Access the data
    response_json = response.json()
    p_value = response_json["p_value"]
    slope = response_json["slope"]

    # Output results
    print(f"IV: {iv}, DV: {dv}, P-value: {p_value}, Slope: {slope}")
```

At this point, I had everything I needed to do the final step and observe some P-values! So, I called all the functions needed and chose some variable pairs. I opted to create a list with dictionaries of the independent variable and dependent variables just to make the code cleaner.

```python
if __name__ == '__main__':
    country_year_list = get_country_year_list()
    file_id = get_file_id(country_year_list)
    
    iv_dv_pairs = [{"iv": "access_to_electricity", "dv": "undernourishment"},
                   {"iv": "cereal_yield", "dv": "undernourishment"},
                   {"iv": "literacy_rate", "dv": "inflation"}, {"iv": "cereal_yield", "dv": "inflation"},
                   {"iv": "undernourishment", "dv": "literacy_rate"}]

    # Iterating through all of the pairs
    for iv_dv_pair in iv_dv_pairs:
        analyze_data(iv_dv_pair["iv"], iv_dv_pair["dv"], file_id)
```

 ### Findings

 My code yielded me the following results:

| Independent Variable | Dependent Variable | P-value | Slope |
| :------------------: | :----------------: | :-----: | :---: |
| Access to electricity|  Undernourishment  |   0.0   | -0.27567969799406133 |
|      Cereal yield    |  Undernourishment  |   0.0   | -0.0015923644834796352 |
|     Literacy rate    |     Inflation      | 0.8315253078800389 | 0.006366144354506915|
|    Cereal yield      |     Inflation      | 0.007478519871229272 | -0.0037016306110891246|
|   Undernourishment   |    Literacy rate   |   0.0   | -0.9595336780164104
|

I got quite a few P-values of 0.0, which I am pretty sure is because of some value rounding that occurs in Python. Regardless, the pairs of __(1) access to electricity and undernourishment__, __(2) cereal yield and undernourishment__, __(3) cereal yield and inflation__,  and __(4) undernourishment and literacy rate__ had high statistical significance. I am generally not very surprised by the results: (1) you would expect that people with more access to electricity are probably in more developed nations with less undernourishment (2) the more cereal (food) that a country yields, the more nourished its population will be and (4) the more people are fed, the more time they have to receive an education. However, I could not quite account for the relationship between __(3) cereal yield and inflation__. I would have thought that the more prosperous a country is in terms of resources, the less likely it is to experience inflation. I did not really know what to expect with __literacy rate and inflation__, so that result was not very surprising. Nonetheless, it is cool to see that there is not much statistical significance between them (I resisted the urge to P-hack--hopefully my publication won't perish). I took some of the findings with a grain of salt because some indicators, namely undernourishment, did not have much data. 

# Conclusion

This lab was pretty challenging! I felt more comfortable with *part 2* after having completed *part 1*. This lab shows yet again the power of nested dictionaries--I'll definitely use them more often! Thanks for your help Ms. Feng and hopefully I didn't clutter your server with API key requests :)