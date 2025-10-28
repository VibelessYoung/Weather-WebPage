# Weather

یک وب‌سایت پیش‌بینی وضعیت آب‌وهوا که می‌تونه وضعیت فعلی و پیش‌بینی ۵ روز آینده رو نمایش بده. این پروژه با استفاده از HTML، CSS و JavaScript ساخته شده و از موقعیت جغرافیایی کاربر برای نمایش اطلاعات استفاده می‌کنه.

---

# ویژگی ها
- نمایش وضعیت فعلی آب‌وهوا بر اساس موقعیت جغرافیایی کاربر
- پیش‌بینی وضعیت آب‌وهوا برای ۵ روز آینده
- طراحی ساده و کاربرپسند
- استفاده از API برای دریافت داده‌های آب‌وهوا

---

# نکات و شروع
فایل JS ایگنور شده به دلیل اینکه کسی از API من استفاده نکنه ! بنابراین برای شروع پروژه رو کلون کنید و فایل اسکریپت رو با استفاده از کد پایین بسازید.

---

# JS Code
/* START */

document.addEventListener("DOMContentLoaded", function () {
    const menuIcon = document.querySelector(".mobile-menu-icon");
    const navList = document.querySelector(".nav-ul");

    menuIcon.addEventListener("click", () => {
      navList.classList.toggle("active");
    });
  });

  /* API */

    const API_KEY = 'Your API'; // OpenWeatherMap

    /* DATA */

    const weatherBox = document.getElementById('weather-box');
    const forecastBox = document.getElementById('forecast-box');
    const forecastContainer = document.getElementById('forecast-container');

    const btnGet = document.getElementById('get-location');
    const btnSave = document.getElementById('save-local');
    const btnReset = document.getElementById('reset');

    /* SHOW */

    function renderWeather(data) {
      weatherBox.innerHTML = `
        <h2>${data.name}</h2>
        <p>دما: ${data.main.temp}°C</p>
        <p>رطوبت: ${data.main.humidity}%</p>
        <p>وضعیت: ${data.weather[0].description}</p>
        <img src="https://openweathermap.org/img/wn/${data.weather[0].icon}@2x.png" alt="">
      `;
      weatherBox.style.display = 'block';
    }

    function renderForecast(data) {
      forecastContainer.innerHTML = '';
      const filtered = data.list.filter((item, i) => item.dt_txt.includes("12:00:00")); // فقط ظهر

      filtered.forEach(item => {
        const date = new Date(item.dt_txt).toLocaleDateString('fa-IR');
        forecastContainer.innerHTML += `
          <div class="forecast-item">
            <p>${date}</p>
            <img src="https://openweathermap.org/img/wn/${item.weather[0].icon}.png" alt="">
            <p>${item.main.temp}°C</p>
            <p>${item.weather[0].description}</p>
          </div>
        `;
      });
      forecastBox.style.display = 'block';
    }

    function getWeather(lat, lon) {
      fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&units=metric&lang=fa&appid=${API_KEY}`)
        .then(res => res.json())
        .then(data => {
          renderWeather(data);
          localStorage.setItem('weather', JSON.stringify(data));
        });

      fetch(`https://api.openweathermap.org/data/2.5/forecast?lat=${lat}&lon=${lon}&units=metric&lang=fa&appid=${API_KEY}`)
        .then(res => res.json())
        .then(data => {
          renderForecast(data);
          localStorage.setItem('forecast', JSON.stringify(data));
        });
    }

    /* LOC EROR */

    function getLocationAndWeather() {
      navigator.geolocation.getCurrentPosition(
        pos => {
          const { latitude, longitude } = pos.coords;
          getWeather(latitude, longitude);
        },
        () => alert('Failed to get loacation')
      );
    }

    /* SAVE */

    btnGet.onclick = getLocationAndWeather;

    btnSave.onclick = () => {
      alert('Successful✅');
    };

    /* RESET */

    btnReset.onclick = () => {
      localStorage.removeItem('weather');
      localStorage.removeItem('forecast');
      weatherBox.style.display = 'none';
      forecastBox.style.display = 'none';
    };

    /* LOAD FROM LOCAL STORAGE */

    window.onload = () => {
      const weather = localStorage.getItem('weather');
      const forecast = localStorage.getItem('forecast');

      if (weather) {
        renderWeather(JSON.parse(weather));
      }
      if (forecast) {
        renderForecast(JSON.parse(forecast));
      }
    };
    
