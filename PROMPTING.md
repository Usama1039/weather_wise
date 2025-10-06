# Before/After Code Improvement Examples

## Example 1: Weather Data Error Handling

### Initial Prompt:
"You, as a software developer, let's implement the get_weather_data function using the wttr.in API. Check the below sample code for reference as well:
```python
def get_weather_data(location, forecast_days=5):
    '''
    Retrieve weather data for a specified location.
    Args:
        location (str): City or location name
        forecast_days (int): Number of days to forecast (1-5)
    Returns:
        dict: Weather data including current conditions and forecast
    '''
    pass
```

### Follow-up Prompt (That Led to Improvements):
"I have tested this code, which is working for normal use cases, but what will happen when:
- Invalid City like `data = get_weather_data("FakeCity123", forecast_days=3)`
- Empty Location like `data = get_weather_data("", forecast_days=3)`
- Bad forecast_days like `data = get_weather_data("Sydney", forecast_days=10)`
- Special Characters like `data = get_weather_data("São Paulo", forecast_days=2)`"

### BEFORE - Original Implementation:
```python
def get_weather_data(location, forecast_days=5):
    """
    Retrieve weather data for a specified location from wttr.in API.

    Args:
        location (str): City or location name.
        forecast_days (int): Number of days to forecast (1-5).

    Returns:
        dict: Weather data including current conditions and forecast.
    """
    # Safety check for days
    if forecast_days < 1 or forecast_days > 5:
        raise ValueError("forecast_days must be between 1 and 5")

    try:
        url = f"https://wttr.in/{location}?format=j1"
        response = requests.get(url)
        response.raise_for_status()  # Raise error for bad response
        raw_data = response.json()

        # Extract current condition
        current = raw_data.get("current_condition", [])[0]

        # Extract forecast days
        forecast = raw_data.get("weather", [])[:forecast_days]

        # Restructure into clean dict
        weather_data = {
            "location": location,
            "current": {
                "temperature_C": current.get("temp_C"),
                "condition": current.get("weatherDesc", [{}])[0].get("value"),
                "humidity": current.get("humidity"),
                "feels_like_C": current.get("FeelsLikeC"),
            },
            "forecast": []
        }

        for day in forecast:
            day_info = {
                "date": day.get("date"),
                "max_temp_C": day.get("maxtempC"),
                "min_temp_C": day.get("mintempC"),
                "hourly": []
            }

            for hour in day.get("hourly", []):
                day_info["hourly"].append({
                    "time": hour.get("time"),
                    "temp_C": hour.get("tempC"),
                    "precip_mm": hour.get("precipMM"),
                    "chance_of_rain": hour.get("chanceofrain"),
                    "condition": hour.get("weatherDesc", [{}])[0].get("value"),
                })

            weather_data["forecast"].append(day_info)

        return weather_data

    except requests.RequestException as e:
        print(f"Error fetching data from wttr.in: {e}")
        return {}
```

### AFTER - Improved Implementation:
```python
def get_weather_data(location, forecast_days=5):
    """
    Retrieve weather data for a specified location from wttr.in API.
    Handles edge cases like invalid input and special characters.
    """
    # Validate inputs
    if not location.strip():
        raise ValueError("Location cannot be empty")
    if forecast_days < 1 or forecast_days > 5:
        raise ValueError("forecast_days must be between 1 and 5")

    try:
        url = f"https://wttr.in/{quote(location)}?format=j1"
        response = requests.get(url)
        response.raise_for_status()
        raw_data = response.json()

        # Check for valid response
        if "current_condition" not in raw_data or not raw_data["current_condition"]:
            print(f"No weather data available for '{location}'.")
            return {}

        current = raw_data["current_condition"][0]
        forecast = raw_data.get("weather", [])[:forecast_days]

        weather_data = {
            "location": location,
            "current": {
                "temperature_C": current.get("temp_C"),
                "condition": current.get("weatherDesc", [{}])[0].get("value"),
                "humidity": current.get("humidity"),
                "feels_like_C": current.get("FeelsLikeC"),
            },
            "forecast": []
        }

        for day in forecast:
            day_info = {
                "date": day.get("date"),
                "max_temp_C": day.get("maxtempC"),
                "min_temp_C": day.get("mintempC"),
                "hourly": []
            }

            for hour in day.get("hourly", []):
                day_info["hourly"].append({
                    "time": hour.get("time"),
                    "temp_C": hour.get("tempC"),
                    "precip_mm": hour.get("precipMM"),
                    "chance_of_rain": hour.get("chanceofrain"),
                    "condition": hour.get("weatherDesc", [{}])[0].get("value"),
                })

            weather_data["forecast"].append(day_info)

        return weather_data

    except requests.RequestException as e:
        print(f"Error fetching data from wttr.in: {e}")
        return {}
```

### Why the Prompting Was Effective:
- **Identified specific failure scenarios** instead of generic "make it better"
- **Asked about edge cases** that weren't handled in the original code
- **Provided concrete test cases** that would expose the weaknesses
- **Focused on real-world problems** like special characters and invalid inputs

### Key Improvements Made:
1. **Input validation** for empty location strings
2. **URL encoding** using `quote()` for special characters
3. **Response validation** to check for valid API responses
4. **Better error messages** that inform users about specific issues

---

## Example 2: Response Generation Key Mismatch

### Initial Prompt:
"I need to make the last main function for my weather project which is generate_weather_response.

I have two pieces of information:
- parsed_info: a dictionary from the last function that tells me what the user asked for (like {'intent': 'rain', 'location': 'Tokyo', 'date': 'tomorrow'}).
- weather_data: a big dictionary of actual weather numbers from the internet.

Can you please write this function for me? It should look at the intent (like 'rain' or 'temperature') and then use the weather_data to create a simple, friendly sentence answer.

For example, if the intent is 'rain', it should return something like:
'Yes, there is a 60% chance of rain in Tokyo tomorrow.'

And below is the sample function:
```python
def generate_weather_response(parsed_question, weather_data):
    '''
    Generate a natural language response to a weather question.
    Args:
        parsed_question (dict): Parsed question data
        weather_data (dict): Weather data
    Returns:
        str: Natural language response
    '''
    pass
```

### Follow-up Prompt (That Led to Improvements):
"My generate_weather_response function is returning 'Sorry, I couldn't fetch weather data for tokyo' but I know the data is there. The issue seems to be that the function is looking for the wrong keys in the weather_data dictionary. Can you fix it to use the correct key names that match my get_weather_data function's output?"

### BEFORE - Mismatched Key Names:
```python
def generate_weather_response(parsed_question, weather_data):
    """
    Generate a natural language response to a weather question.

    Args:
        parsed_question (dict): Parsed question data, e.g.
            {'intent': 'rain', 'location': 'Tokyo', 'date': '2025-09-22'}
        weather_data (dict): Weather data returned by get_weather_data()

    Returns:
        str: Natural language response
    """
    # Extract useful bits from parsed_question
    intent = parsed_question.get("intent")
    location = parsed_question.get("location", "the specified location")
    date = parsed_question.get("date")

    if not weather_data or "weather" not in weather_data:
        return f"Sorry, I couldn't fetch weather data for {location}."

    # Pick which day's forecast to use
    forecast_days = weather_data.get("weather", [])
    chosen_day = None
    for day in forecast_days:
        if day.get("date") == date:
            chosen_day = day
            break

    if not chosen_day:
        return f"Sorry, I couldn't find a forecast for {date} in {location}."

    # Now build the response depending on intent
    if intent == "rain" or intent == "precipitation":
        chance = chosen_day.get("hourly", [])[4].get("chanceofrain", "0")  # noon-ish slot
        return f"In {location} on {date}, there is a {chance}% chance of rain."

    elif intent == "temperature":
        avg_temp = chosen_day.get("avgtempC", "N/A")
        return f"The average temperature in {location} on {date} will be around {avg_temp}°C."

    elif intent == "weather":
        description = chosen_day.get("hourly", [])[4].get("weatherDesc", [{}])[0].get("value", "unknown")
        return f"The weather in {location} on {date} is expected to be {description}."

    else:
        # fallback for unrecognized intent
        return f"Here's the forecast for {location} on {date}: {chosen_day.get('avgtempC', 'N/A')}°C average, with a {chosen_day.get('hourly', [])[4].get('chanceofrain', '0')}% chance of rain."
```

### AFTER - Corrected Key Names:
```python
def generate_weather_response(parsed_question, weather_data):
    """
    Generate a natural language response to a weather question.

    Args:
        parsed_question (dict): Parsed question data
        weather_data (dict): Weather data (parsed into 'location', 'current', 'forecast')

    Returns:
        str: Natural language response
    """
    intent = parsed_question.get("intent")
    location = parsed_question.get("location", "the specified location")
    date = parsed_question.get("date")

    # Safety: check if we even have a forecast
    forecast_days = weather_data.get("forecast", [])
    chosen_day = None
    for day in forecast_days:
        if day.get("date") == str(date):   # match exact date
            chosen_day = day
            break

    if not chosen_day:
        return f"Sorry, I couldn't find a forecast for {date} in {location}."

    # Handle intents
    if intent in ["rain", "precipitation"]:
        # take the last hourly slot (usually night) with rain chance
        hourly = chosen_day.get("hourly", [])
        if hourly:
            best_slot = max(hourly, key=lambda h: int(h.get("chance_of_rain", 0)))
            chance = best_slot.get("chance_of_rain", "0")
            condition = best_slot.get("condition", "").strip()
            return f"In {location.title()} on {date}, there is a {chance}% chance of rain ({condition})."
        return f"In {location.title()} on {date}, I couldn't find rain information."

    elif intent == "temperature":
        max_temp = chosen_day.get("max_temp_C", "N/A")
        min_temp = chosen_day.get("min_temp_C", "N/A")
        return f"The temperature in {location.title()} on {date} will range from {min_temp}°C to {max_temp}°C."

    elif intent == "weather":
        hourly = chosen_day.get("hourly", [])
        if hourly:
            mid_day = hourly[len(hourly)//2]
            condition = mid_day.get("condition", "unknown").strip()
            return f"The weather in {location.title()} on {date} is expected to be {condition}."
        return f"The weather forecast in {location.title()} on {date} is unavailable."

    else:
        # fallback summary
        return (
            f"In {location.title()} on {date}, "
            f"temps will range {chosen_day.get('min_temp_C', 'N/A')}°C–{chosen_day.get('max_temp_C', 'N/A')}°C "
            f"with some chance of rain."
        )
```

### Why the Prompting Was Effective:
- **Identified the specific problem** (key mismatch) rather than general debugging
- **Provided context** about what was expected vs. what was happening
- **Referenced the connection** between two related functions
- **Asked for alignment** between data structures

### Key Improvements Made:
1. **Corrected key names** from `"weather"` to `"forecast"`
2. **Better error handling** for missing hourly data
3. **Improved logic** for finding the best rain chance slot
4. **Enhanced responses** with location capitalization and better formatting
5. **More robust date matching** with string conversion