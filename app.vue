<script lang="ts" setup>

import { computed, ref, useHead } from "#imports";

useHead({
  title: 'Створіть власне посилання збору коштів для фонду Повернись Живим',
  link: [
    {'rel': 'icon', 'type': 'image/png', 'sizes': '32x32', 'href': '/favicon-32x32.png'},
    {'rel': 'icon', 'type': 'image/png', 'sizes': '16x16', 'href': '/favicon-16x16.png'},
  ],
  meta: [
    {name: 'robots', content: 'index,follow'},
    {
      name: 'description',
      content: 'Тут ви можете створити закрите посилання на контент, який буде доступний лише після донату у фонд Повернись Живим',
    },
  ],
});

const amount = ref(50);
const currency = ref('UAH');
const tag = ref('');
const next = ref('');


const isValidNextURL = computed(() => {
  if (!next.value.trim()) {
    return false;
  }

  if (!next.value.trim().startsWith('http')) {
    return false;
  }

  try {
    new URL(next.value);
    return true;
  } catch (_) {
    return false;
  }
});

const isFormValid = computed(() =>
    amount.value > 0
    && ['UAH', 'USD', 'EUR'].includes(currency.value)
    && isValidNextURL.value,
);


const donationUrl = computed(() => {
  const baseURL = new URL('https://subscribe.comebackalive.in.ua/pay');
  baseURL.searchParams.set('amount', amount.value.toString());
  baseURL.searchParams.set('currency', currency.value.toString());
  baseURL.searchParams.set('tag', tag.value.toString());
  baseURL.searchParams.set('next', next.value.toString());

  return isFormValid.value ? baseURL.toString() : '';
});


const reportUrl = computed(() => {
  const baseURL = new URL('https://report.comebackalive.in.ua/public/dashboard/a297a107-6740-4b69-9603-f91703f7afba');
  baseURL.searchParams.set('tag', tag.value.toString());

  return (isFormValid.value && !!tag.value?.trim()) ? baseURL.toString() : '';
});
</script>

<template>
  <div class="container py-3">

    <h1>
      Створіть власне посилання збору коштів для фонду
      <br>
      <a href="https://savelife.in.ua/" target="_blank">Повернись Живим</a>
    </h1>

    <p>
      Тут ви можете створити закрите посилання на контент, який буде доступний лише після донату у фонд Повернись Живим.
    </p>

    <details class="mb-3">
      <summary>Як це працює</summary>
      <ol>
        <li>Ви заповнюєте форму нижче вказавши куди користувач повинен потрапити після донату та розмір донату.</li>
        <li>Під формою для вас буде згенеровано спеціальна URL адреса.</li>
        <li>Перейшовши за цією адресою, вашому підписнику буде запропоновано внести донат у фонд Повернись Живим визначеного розміру.</li>
        <li>Після того як оплату буде зараховано його автоматично перенаправить за адресою призначення.</li>
      </ol>
    </details>

    <form class="was-validated mb-3">

      <div class="mb-3">
        <label for="next" class="form-label">Адреса призначення</label>
        <input type="url"
               v-model="next"
               id="next"
               class="form-control"
               aria-describedby="next-help"
               required
               placeholder="https://…">
        <div id="next-help" class="form-text">
          На цю адресу буде переспрямовано людину після донату. Це може бути посилання на відео, арт, статтю, архів файлів будь-де, закриту групу тощо.
          Швидко створити сторінку в інтернеті можна тут: <a href="https://telegra.ph/" target="_blank">telegra.ph</a>.
        </div>
      </div>

      <div class="mb-3">
        <label for="amount" class="form-label">Валюта та розмір донату</label>
        <div class="input-group mb-3">
          <select class="input-group-text" v-model="currency" required>
            <option>UAH</option>
            <option>USD</option>
            <option>EUR</option>
          </select>
          <input type="number" class="form-control" required v-model="amount" id="amount">
        </div>
      </div>

      <div class="mb-3">
        <label for="tag" class="form-label">Унікальний тег</label>
        <input type="search" v-model="tag" class="form-control" id="tag" aria-describedby="tag-help">
        <div id="tag-help"
             class="form-text">Слугує для побудови звітів. Це може бути будь який текст. Всі донати з однаковим тегом будуть об’єднані в один звіт.
        </div>
      </div>


      <hr>


      <div class="mb-3">
        <label for="donationUrl" class="form-label">Посилання на збір </label>
        <div class="input-group">
          <input type="url"
                 readonly
                 class="form-control"
                 id="donationUrl"
                 :value="donationUrl"
                 aria-describedby="donationUrl-help">
          <a target="_blank"
             v-if="donationUrl"
             :href="donationUrl"
             class="input-group-text"
             title="Перейти за посиланням">🔗</a>
        </div>
        <div id="donationUrl-help" class="form-text">
          <b>Публікуйте це посилання в соц.мережах</b>.
          Перейшовши за ним, відвідувач спочатку внесе донат і після зарахування коштів буде переспрямований на адресу призначення
        </div>
      </div>


      <div class="mb-3">
        <label for="reportUrl" class="form-label">Звіт всіх донатів за тегом тут</label>
        <div class="input-group">
          <input type="url"
                 readonly
                 class="form-control"
                 id="reportUrl"
                 :value="reportUrl"
                 aria-describedby="reportUrl-help">
          <a target="_blank"
             v-if="reportUrl"
             :href="reportUrl"
             class="input-group-text"
             title="Перейти за посиланням">🔗</a>
        </div>
        <div id="reportUrl-help" class="form-text">
          <b>Цей URL для вас</b>.
          За ним ви можете подивитись загальну суму донатів за тегом.
        </div>
      </div>
    </form>

    <footer>
      <p>
        Цей сайт створено волонтерами. Він не є офіційним ресурсом фонду Повернись живим.
      </p>

      <a href="https://github.com/cawa-93/comebackalive-paywall" target="_blank">
        <svg aria-label="GitHub" width="16" height="16" fill="currentColor" viewBox="0 0 16 16">
          <path d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.012 8.012 0 0 0 16 8c0-4.42-3.58-8-8-8z"/>
        </svg>
        Зворотній зв’язок
      </a>
    </footer>

  </div>
</template>

<style lang="scss">

$container-max-widths : (
    sm: 540px,
    md: 720px
);

$h1-font-size         : 2rem;

// Functions first
//@import "../node_modules/bootstrap/scss/bootstrap";

// 1. Include functions first (so you can manipulate colors, SVGs, calc, etc)
@import "../node_modules/bootstrap/scss/functions";

// 2. Include any default variable overrides here

// 3. Include remainder of required Bootstrap stylesheets
@import "../node_modules/bootstrap/scss/variables";

// 4. Include any default map overrides here

// 5. Include remainder of required parts
@import "../node_modules/bootstrap/scss/maps";
@import "../node_modules/bootstrap/scss/mixins";
@import "../node_modules/bootstrap/scss/root";

//// Optional components
//@import "../node_modules/bootstrap/scss/utilities";
@import "../node_modules/bootstrap/scss/reboot";
@import "../node_modules/bootstrap/scss/containers";
@import "../node_modules/bootstrap/scss/forms";
@import "../node_modules/bootstrap/scss/vendor/rfs";


h1 {
  @include margin-bottom(3rem);
  text-align : center;
}


.py-3 {
  padding-block : 1rem;
}


.mb-3 {
  margin-bottom : 1rem;
}


footer {
  text-align : center;
  @include margin-top(5rem);
}


a:not(:hover, :focus-visible) {
  text-decoration : none;
}


a[href="https://github.com/cawa-93/comebackalive-paywall"] {
  margin-left : auto;
  display     : inline-flex;
  align-items : center;
  gap         : 0.5em;
  font-size   : 0.8em;
  color       : inherit;

  &:not(:hover, :focus-visible) {
    opacity : 0.5;
  }
}
</style>
