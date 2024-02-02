<script setup>
import { computed, ref, watch } from 'vue';
import axios from 'axios';

    const city = ref("");
    const weatherData = ref(null);
    const errorMessage = ref("");
    const isLoading = ref(false);

    const handleEnterKey = (e) => {
        city.value = e.target.value.trim();
        e.target.value = "";
    };

    const url = computed(() => {
        return `https://api.openweathermap.org/data/2.5/weather?units=metric&lang=ru&q=${city.value}&appid=${import.meta.env.VITE_API_KEY}`
    })

    watch(city, () => {
        isLoading.value = true;
        weatherData.value = null;
        errorMessage.value = "";
        
        axios
            .get(url.value)
            .then(res => {
                weatherData.value = res.data;
            })
            .catch(e => {
                console.log(e)
                if(e.response.status === 404) {
                    errorMessage.value = "Информация отсутствует!"
                } else {
                    errorMessage.value = "Ошибка сервера! Попробуйте позже ..."
                }
            })
            .finally(() => isLoading.value = false);
    })
</script>

<template>
    <div class="wrapper">
        <input 
            class="input"
            type="text"
            placeholder="Введите город"
            :disabled="isLoading"
            @keyup.enter="handleEnterKey($event)"
        />
        <div v-if="isLoading" class="wrapper-loader">
            <span class="loader"></span>
        </div>
        <div v-else-if="errorMessage" class="error">{{ errorMessage }}</div>
        <div v-else-if="weatherData" class="info">
            <div class="block-left">
                <div class="temperature">{{ Math.round(weatherData.main?.temp) }} &#176;C</div>
                <div class="city">{{ weatherData.name }} {{ weatherData.sys?.country }}</div>
            </div>
            <div class="block-right">
                <img 
                    :src="'https://openweathermap.org/img/wn/' + (weatherData.weather[0]?.icon) + '.png'" 
                    alt="Weather Icon" 
                />
                <div class="description">{{ weatherData.weather[0]?.description }}</div>
            </div>
        </div>
    </div>
</template>

<style lang="css" scoped>
    .wrapper {
        background-color: #1b2223;
        border-radius: 14px;
        padding: 30px;
        display: flex;
        flex-direction: column;
        row-gap: 30px;
        max-width: 320px;
        width: 100%;
    }

    .input {
        background-color: #4a4a4a;
        border-radius: 14px;
        font-size: 18px;
        width: 100%;
        padding: 10px 20px;
    }

    .info {
        display: flex;
    }

    .block-left {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        flex: 1 0 50%;
        row-gap: 10px;
    }

    .temperature {
        font-size: 33px;
    }

    .city {
        font-size: 20px;
    }

    .block-right {
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        align-items: center;
        flex: 1 0 50%;
    }

    .description {
        text-align: center;
    }

    .description::first-letter {
        text-transform: uppercase;
    }

    .error {
        max-width: 320px;
        width: 100%;
        text-align: center;
        line-height: 30px;
        letter-spacing: 1.5px;
    }

    .wrapper-loader {
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .loader {
        width: 48px;
        height: 48px;
        border: 5px solid #FFF;
        border-bottom-color: transparent;
        border-radius: 50%;
        display: inline-block;
        box-sizing: border-box;
        animation: rotation 1s linear infinite;
    }

    @keyframes rotation {
        0% {
            transform: rotate(0deg);
        }
        100% {
            transform: rotate(360deg);
        }
    } 
</style>
