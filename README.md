# weather-app
npm install express axios cors
const express = require('express');
const axios = require('axios');
const cors = require('cors');
const app = express();
app.use(cors());
app.use(express.json());

const API_KEY = 'YOUR_OPENWEATHERMAP_API_KEY'; // Get from https://openweathermap.org/api

app.get('/weather', async (req, res) => {
    const city = req.query.city;
    if (!city) return res.status(400).json({ error: 'City is required' });

    try {
        const response = await axios.get(
            `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${API_KEY}&units=metric`
        );
        res.json(response.data);
    } catch (error) {
        res.status(500).json({ error: 'City not found or API error' });
    }
});

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weather App</title>
    <style>
        body { font-family: Arial; text-align: center; padding: 50px; background: #f0f8ff; }
        input, button { padding: 10px; font-size: 16px; }
        .weather { margin-top: 20px; }
    </style>
</head>
<body>
    <h1>Weather App</h1>
    <input type="text" id="city" placeholder="Enter city name">
    <button onclick="getWeather()">Get Weather</button>

    <div class="weather" id="weather"></div>

    <script>
        async function getWeather() {
            const city = document.getElementById('city').value;
            if (!city) return alert('Enter a city name');

            try {
                const res = await fetch(`http://localhost:5000/weather?city=${city}`);
                const data = await res.json();

                if (data.error) {
                    document.getElementById('weather').innerHTML = data.error;
                    return;
                }

                document.getElementById('weather').innerHTML = `
                    <h2>${data.name}, ${data.sys.country}</h2>
                    <p>Temperature: ${data.main.temp} °C</p>
                    <p>Weather: ${data.weather[0].description}</p>
                    <p>Humidity: ${data.main.humidity}%</p>
                    <p>Wind Speed: ${data.wind.speed} m/s</p>
                `;
            } catch (err) {
                document.getElementById('weather').innerHTML = 'Error fetching weather data';
            }
        }
    </script>
</body>
</html>
