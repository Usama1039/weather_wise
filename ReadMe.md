# 🌦️ WeatherWise - Smart Weather Assistant

## Overview

WeatherWise is an intelligent weather advisor application that combines real-time weather data with natural language processing capabilities. The application allows users to ask weather questions in plain English and receive conversational responses, along with interactive visualizations of weather forecasts.

**Key Features:**
- Fetches live weather data using the **wttr.in API** (no API key required)
- Parses natural language weather questions (e.g., *"Will it rain in Tokyo tomorrow?"*)
- Generates human-readable responses with contextual advice
- Creates interactive temperature and precipitation visualizations
- Provides an intuitive menu-driven interface using `pyinputplus`

---

## Core Functionality

### 1. Weather Data Retrieval
- **`get_weather_data(location, forecast_days=5)`**
  - Fetches current conditions and multi-day forecasts from wttr.in API
  - Handles input validation, error cases, and special characters
  - Returns structured weather data with current conditions and hourly forecasts

### 2. Natural Language Processing
- **`parse_weather_question(user_question)`**
  - Extracts intent (precipitation, temperature, general conditions)
  - Identifies location mentions and time periods
  - Supports complex queries with date parsing (today, tomorrow, specific weekdays)

### 3. Response Generation
- **`generate_weather_response(parsed_question, weather_data)`**
  - Converts structured data into natural, conversational responses
  - Provides contextual advice (e.g., umbrella recommendations)
  - Handles various intent types with appropriate formatting

### 4. Data Visualizations
- **`create_temperature_visualisation(weather_data)`** 
  - Line graph showing min/max temperature trends over forecast period
  - Color-coded temperature ranges with data labels
- **`create_precipitation_visualisation(weather_data)`**
  - Bar chart displaying daily precipitation chances
  - Color-coded risk levels (low/medium/high chance of rain)

### 5. Interactive Interface
- Menu-driven system with options for:
  - Natural language weather queries
  - Temperature forecast charts
  - Precipitation forecast charts
  - Easy navigation and error handling

---

## Installation & Setup

### Prerequisites
- Python 3.8 or higher
- Internet connection for weather API access

### Required Dependencies
```bash
pip install requests matplotlib pyinputplus
```

### Running the Application
1. Clone or download the project files
2. Open the Jupyter notebook: `starter_notebook.ipynb`
3. Run all cells to initialize the functions
4. Execute the main interface cell to start the application

---

## Usage Examples

### Interactive Menu System
```
==================================================
 🌦 Welcome to the Weather Assistant 🌦
==================================================

What would you like to do?
Select an option:
1. Ask a weather question
2. Show temperature chart
3. Show precipitation chart
4. Exit
```

### Natural Language Queries
- **"Will it rain tomorrow in Sydney?"** → *"In Sydney on 2025-09-23, there is a 45% chance of rain (Light rain)."*
- **"What's the temperature in London today?"** → *"The temperature in London on 2025-09-22 will range from 9°C to 17°C."*
- **"Weather conditions in Tokyo this weekend?"** → *"The weather in Tokyo on 2025-09-23 is expected to be Partly cloudy."*

### Supported Query Types
- **Location patterns**: "in [city]", "for [city]", "[city] weather"
- **Time references**: today, tomorrow, this weekend, Monday/Tuesday/etc.
- **Weather attributes**: rain, temperature, conditions, precipitation
- **Advice queries**: "Should I bring an umbrella?", "Do I need a jacket?"

---

## Technical Implementation

### Architecture
- **Modular design** with separate functions for each major capability
- **Error handling** throughout with graceful degradation
- **Input validation** for all user inputs and API responses
- **Flexible data structures** supporting various weather data formats

### API Integration
- Uses wttr.in public weather API (no authentication required)
- Handles rate limiting, timeouts, and connection errors
- Supports international locations and special characters
- Processes JSON responses with comprehensive error checking

### Data Processing
- Extracts relevant information from complex weather API responses
- Converts raw data into structured formats suitable for visualization
- Handles missing data fields with appropriate defaults
- Supports multiple forecast days (1-5 day range)

---

## Testing & Validation

The application includes comprehensive test cases covering:

### Input Validation Tests
- Empty/invalid location names
- Out-of-range forecast days
- Malformed user questions
- Special characters and Unicode support

### Natural Language Processing Tests
- Intent detection accuracy
- Location extraction from various sentence structures
- Date parsing for different time expressions
- Complex multi-attribute queries

### API Integration Tests
- Network error handling
- Invalid location responses
- Rate limiting scenarios
- Data structure validation

### Visualization Tests
- Empty data handling
- Various data ranges and edge cases
- Chart formatting and display options

---

## Error Handling & Edge Cases

### Robust Error Management
- **Network failures**: Graceful handling with user-friendly error messages
- **Invalid locations**: Clear feedback when cities cannot be found
- **Missing data**: Appropriate defaults and notifications
- **Malformed queries**: Helpful suggestions for better phrasing

### Input Validation
- Location name sanitization and validation
- Forecast day range enforcement (1-5 days)
- Query length and format checking
- Special character and encoding support

---

## Development Process

This project was developed using **AI-assisted programming techniques**, demonstrating:

- **Strategic prompting** for problem-solving and code generation
- **Iterative refinement** through multiple AI conversations
- **Code review and testing** using AI analysis tools
- **Documentation generation** with AI assistance
- **Quality assurance** through comprehensive testing strategies

The development process emphasized learning to effectively collaborate with AI tools while maintaining ownership of the technical decisions and implementation details.

---



## Requirements Summary

- **Python 3.8+**
- **Core Libraries**: `requests`, `matplotlib`, `pyinputplus`
- **Additional Libraries**: `re`, `datetime`, `calendar` (standard library)
- **API Access**: Internet connection for wttr.in weather service
- **Environment**: Jupyter Notebook or Python script execution

---
