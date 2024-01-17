<script setup>

    import { computed, ref, watch } from 'vue';

    const city = ref("");
    const weatherData = ref(null);
    const loading = ref(false);

    const handleEnterKey = (e) => {
        city.value = e.target.value;
        e.target.value = "";
    };

    const url = computed(() => {
        return `https://api.openweathermap.org/data/2.5/weather?units=metric&lang=ru&q=${city.value}&appid=${import.meta.env.VITE_API_KEY}`
    })

    watch(city, async () => {
        try {
            loading.value = true;

            const res = await fetch(url.value);
            weatherData.value = res.ok ? await res.json() : null;

        } catch (error) {
            weatherData.value = null;
            console.error(error)

        } finally {
            loading.value = false
        }
    })
</script>

<template>
    <div class="wrapper">
        <input 
            class="input"
            type="text"
            placeholder="Введите город"
            :disabled="loading"
            @keyup.enter="handleEnterKey($event)"
        />
        <div class="info" v-if="weatherData">
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
        <div class="error" v-else>Информация отсутствует!</div>
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
</style>
