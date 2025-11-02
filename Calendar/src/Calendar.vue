<!-- Здравствуй, дорогой, техлид или другой(дорогой) разработчик -->
<!-- В общем, я попытался реализовать календарь через ООП и костыли, дабы удивить -->
<!-- Но в итоге удивил себя своей тупостью, так как надо было просто использовать функциональную и реактивную парадигму программирования -->
<!-- К сожалению, у меня просто нехватило времени на реализацию, хотел добавить паттерны(СТРАТЕГИЯ или ШАБЛОННЫЙ МЕТОД) для реализации смены языков -->
<!-- Само собой я хотел переделать архитектуру класса после того, как проверил бы все ли работает(да, знаю, необходимо выбирать архитектуру заранее, дабы маштабироваться было легко) -->
<!-- Вынести класс в composables, а сюда просто импортировать -->
<!-- Потом заняться рефакторингом всего по Мартину(SOLID) (Применить:Вертикальное форматирование, SRP и докинуть правило одной операции в функции, увеличить связанность кода внутри класса, классы разбить и уменьшить уже их связанность и многое другое ) -->
<!-- Так же я бы переделал функцию получения времени, чтобы получать не просто число а полноценную дату, чтобы потом ее синхронизировать с текущей и докинуть стилей для отображения сегодняшнего дня  -->
<!-- И еще много чего нужно было бы переделать или доделать -->
<!-- Мне пришло сообщения от HR в 16:34, а на часах уже 3:31, а мне завтра на работу(да я работаю по воскресеньям) -->
<!-- Обычно в своей практики до меня доходит на следущий день реализация, думаю у вас так же, но у меня нет следующего дня по дедлайну -->
<!-- Благодарю, что обратили на меня внимание, надеюсь еще свидимся -->

<script setup lang="ts">
import { computed, ref } from "vue";
import type { Ref } from "vue";

const localeRuDays = ref<string[]>(["Вс", "Пн", "Вт", "Ср", "Чт", "Пт", "Сб"]);
const localeEnDays = ref<string[]>([
  "Mon",
  "Tue",
  "Wed",
  "Thu",
  "Fri",
  "Sat",
  "Sun",
]);

class MyDate {
  private date: Ref<Date>;

  constructor() {
    this.date = ref(new Date());
  }

  public get getDate() {
    return this.date.value.getDate();
  }

  public get getDay() {
    return this.date.value.getDay();
  }

  public get getMonth() {
    return this.date.value.getMonth();
  }

  public get getYear() {
    return this.date.value.getFullYear();
  }

  public get getRuStringDay() {
    return this.date.value
      .toLocaleString("ru-RU", { weekday: "short" })
      .toUpperCase();
  }

  public get getRuStringMonth() {
    return this.date.value.toLocaleString("ru-RU", { month: "long" });
  }

  public get allMonthDays() {
    const numberOfTotalMonthDays = new Date(
      this.getYear,
      this.getMonth + 1,
      0
    ).getDate();

    return numberOfTotalMonthDays;
  }

  public get totalDaysInMonth() {
    const daysInMonthArray: number[] = [];

    for (let i = 0; i <= this.allMonthDays; i++) {
      daysInMonthArray.push(i);
    }

    return daysInMonthArray;
  }

  public get lastDay() {
    const lastDay = new Date(this.getYear, this.getMonth + 1, 0).getDate();

    return lastDay;
  }

  public get firstDay() {
    const firstDay = new Date(this.getYear, this.getMonth + 1, 1).getDate();

    return firstDay;
  }

  public nextMonth() {
    this.date.value = new Date(this.getYear, this.getMonth + 1);

    return this.date;
  }

  public previousMonth() {
    this.date.value = new Date(this.getYear, this.getMonth - 1);

    return this.date;
  }
}

const myDate = new MyDate();

const calendarDays = computed(() => {
  const days = [];

  const startDayIndex = myDate.firstDay === 0 ? 6 : myDate.firstDay - 1;

  for (let i = 0; i < startDayIndex; i++) {
    days.push({ day: "", isCurrentMonth: false });
  }

  for (let i = 1; i <= myDate.allMonthDays; i++) {
    days.push({ day: i, isCurrentMonth: true });
  }

  const totalCells = days.length;
  const suffixDaysCount = (7 - (totalCells % 7)) % 7;

  for (let i = 0; i < suffixDaysCount; i++) {
    days.push({ day: "", isCurrentMonth: false });
  }

  return days;
});

function check(target: any) {
  console.log(target);
}

const today = new Date().getDate();
</script>

<template>
  <!-- Оставил для отладки -->
  <h1>
    Дата {{ myDate.getDate }} день {{ myDate.getDay }} месяц
    {{ myDate.getMonth }} год {{ myDate.getYear }} Прописной день
    {{ myDate.getRuStringDay }} эксперимент {{ myDate.allMonthDays }} все месяц
    {{ myDate.getRuStringMonth }} Последний день {{ myDate.lastDay }} Первый
    день {{ myDate.firstDay }}
  </h1>

  <div class="calendar">
    <div class="calendar__head">
      <button
        class="calendar__head__button--prev"
        @click="myDate.previousMonth()"
      >
        <
      </button>
      <h2 class="calendar__head__title">
        {{ myDate.getRuStringMonth }} {{ myDate.getYear }}
      </h2>
      <button class="calendar__head__button--next" @click="myDate.nextMonth()">
        >
      </button>
    </div>

    <div class="calendar__body">
      <div class="calendar__body__weekdays">
        <div
          v-for="dayName in localeRuDays"
          :key="dayName"
          class="calendar__weekday--item"
        >
          {{ dayName }}
        </div>
      </div>

      <div class="calendar__dates--grid">
        <div
          v-for="(day, index) in calendarDays"
          :key="index"
          :class="[
            'calendar__date-item',
            { 'calendar__date-item--other-month': !day.isCurrentMonth },
          ]"
        >
          <div
            v-if="day.day"
            :class="['calendar__date--number']"
            @click="check(day.day)"
          >
            {{ day.day }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.calendar {
  max-width: 350px;
  margin: 20px auto;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  overflow: hidden;
  border: 1px solid #e0e0e0;
}

.calendar__head {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 15px;
}
.calendar__head__title {
  margin: 0;
  font-size: 1.1em;
  text-transform: capitalize;
}
.calendar__head__button {
  background: none;
  border: none;
  color: white;
  font-size: 1.5em;
  cursor: pointer;
}

.calendar__body__weekdays,
.calendar__dates--grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  text-align: center;
}

.calendar__body__weekdays {
  font-weight: bold;
}
.calendar__weekday--item {
  padding: 8px 0;
}

.calendar__dates--grid {
  padding: 5px;
}

.calendar__date-item {
  height: 40px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.calendar__date-item--other-month {
  color: #ccc;
}

.calendar__date--number {
  width: 35px;
  height: 35px;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
}

/* Для актального дня зоготовка */
.calendar__date--actual {
  background-color: #e0e0e0;
}

.calendar__date--number:hover {
  border: 1px solid rgb(131, 234, 234);
  border-radius: 0;
}
</style>
