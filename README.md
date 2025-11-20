# Python_project_for_Devops
Python_project_for_Devops


from flask import Flask, render_template_string, request
import requests

app = Flask(__name__)

# HTML Template
html_template = """
<!DOCTYPE html>
<html>
<head>
    <title>Weather App</title>
    <style>
        body { font-family: Arial; margin: 20px; }
        input, select, button { margin: 10px; padding: 8px; }
        #result { margin-top: 20px; font-size: 18px; }
    </style>
</head>
<body>
    <h2>Weather Checker</h2>
    <form method="POST">
        <input type="text" name="name" placeholder="Your Name" required><br>
        <input type="text" name="country" placeholder="Country Code (e.g., IN)" required><br>
        <input type="text" name="city" placeholder="City" required><br>
        <select name="time">
            <option value="morning">Morning</option>
            <option value="afternoon">Afternoon</option>
            <option value="evening">Evening</option>
        </select><br>
        <button type="submit">Get Weather</button>
    </form>
    <div id="result">{{ result }}</div>
</body>
</html>
"""

@app.route("/", methods=["GET", "POST"])
def home():
    result = ""
    if request.method == "POST":
        name = request.form["name"]
        country = request.form["country"]
        city = request.form["city"]
        time = request.form["time"]

        api_key = "YOUR_OPENWEATHERMAP_API_KEY"
        url = f"https://api.openweathermap.org/data/2.5/weather?q={city},{country}&appid={api_key}&units=metric"

        try:
            response = requests.get(url)
            data = response.json()

            if data.get("cod") == 200:
                temp = data["main"]["temp"]
                weather = data["weather"][0]["description"]
                result = f"Hi {name}, in {city} ({country}) this {time}, the temperature is {temp}°C with {weather}."
            else:
                result = f"Error: {data.get('message', 'Unable to fetch weather')}"
        except Exception as e:
            result = f"Failed to fetch weather data: {e}"

    return render_template_string(html_template, result=result)

if __name__ == "__main__":
    app.run(debug=True)
