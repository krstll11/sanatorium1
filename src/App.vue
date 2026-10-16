<script setup>
import { computed, onBeforeUnmount, onMounted, reactive, ref } from 'vue'
import hero from './assets/photos/hero.webp'
import consultation from './assets/photos/consultation.webp'
import gym from './assets/photos/gym.webp'
import pool from './assets/photos/pool.webp'
import massage from './assets/photos/massage.webp'
import lake from './assets/photos/lake.webp'
import room1 from './assets/photos/room1.webp'
import room2 from './assets/photos/room2.webp'
import room3 from './assets/photos/room3.webp'
import food from './assets/photos/food.webp'
import territory from './assets/photos/territory.webp'

const images = {
  hero,
  consultation,
  gym,
  pool,
  massage,
  lake,
  room1,
  room2,
  room3,
  food,
  territory
}

const heroImage = images.hero

const FORMSPREE_ENDPOINT = 'https://formspree.io/f/ВАШ_FORM_ID'

const isScrolled = ref(false)
const activeModal = ref(null)
const openFaq = ref(null)

const programCards = [
  {
    id: 'consultation',
    title: 'Консультация врача',
    text: 'Осмотр, сбор анамнеза и индивидуальный план процедур.',
    image: images.consultation,
    details:
      'Врач оценивает жалобы, ограничения движения, анамнез и противопоказания. По результатам консультации формируется индивидуальный план программы.'
  },
  {
    id: 'gym',
    title: 'Лечебная гимнастика',
    text: 'Упражнения для осанки, подвижности и мышечного корсета.',
    image: images.gym,
    details:
      'Занятия проводятся в малых группах или индивидуально. Нагрузка подбирается с учётом состояния позвоночника и суставов.'
  },
  {
    id: 'pool',
    title: 'Аква-занятия',
    text: 'Упражнения в бассейне с сниженной нагрузкой на суставы.',
    image: images.pool,
    details:
      'Вода уменьшает осевую нагрузку и помогает мягко прорабатывать подвижность. Формат и длительность занятий определяются врачом.'
  },
  {
    id: 'massage',
    title: 'Массаж и физиотерапия',
    text: 'Процедуры для снятия мышечного напряжения и улучшения самочувствия.',
    image: images.massage,
    details:
      'В программу могут входить ручной массаж, аппаратные физиотерапевтические методики и тепловые процедуры. Конкретный набор назначает врач.'
  },
  {
    id: 'lake',
    title: 'Прогулки у озера',
    text: 'Ежедневная активность и восстановление в природной среде.',
    image: images.lake,
    details:
      'Спокойные маршруты по территории и берегу озера дополняют лечебную программу и помогают поддерживать режим дня.'
  }
]

const stayCards = [
  {
    id: 'standard',
    title: 'Стандарт',
    text: 'От 7 900 ₽ за ночь за человека с лечением и питанием.',
    image: images.room1,
    details:
      'Двухместный номер, санузел, телевизор, Wi-Fi. В стоимость включены проживание, трёхразовое питание и базовая лечебная программа.'
  },
  {
    id: 'comfort',
    title: 'Комфорт',
    text: 'От 9 900 ₽ за ночь за человека с лечением и питанием.',
    image: images.room2,
    details:
      'Более просторный номер, обновлённая отделка, зона отдыха. В стоимость включены проживание, питание и расширенная программа процедур.'
  },
  {
    id: 'suite',
    title: 'Полулюкс',
    text: 'От 12 900 ₽ за ночь за человека с лечением и питанием.',
    image: images.room3,
    details:
      'Номер повышенной комфортности, вид на озеро или парк при наличии. В стоимость включены проживание, питание и индивидуальный план восстановления.'
  },
  {
    id: 'food',
    title: 'Питание',
    text: 'Трёхразовое питание включено в стоимость программы.',
    image: images.food,
    details:
      'Сбалансированное меню с учётом рекомендаций врача. Возможны диетические варианты по предварительной договорённости.'
  },
  {
    id: 'territory',
    title: 'Территория',
    text: 'Парк, набережная и прогулочные маршруты у озера.',
    image: images.territory,
    details:
      'Территория подходит для спокойных прогулок, утренней активности и отдыха между процедурами.'
  }
]

const pricing = [
  {
    name: 'Краткий курс',
    duration: '7 ночей',
    price: 'от 55 300 ₽',
    perNight: 'от 7 900 ₽ / ночь',
    includes: [
      'Проживание в номере «Стандарт»',
      'Трёхразовое питание',
      'Консультация врача',
      'До 4 процедур в день',
      'Доступ к территории и прогулочным маршрутам'
    ]
  },
  {
    name: 'Основной курс',
    duration: '10 ночей',
    price: 'от 89 000 ₽',
    perNight: 'от 8 900 ₽ / ночь',
    includes: [
      'Проживание в номере «Комфорт»',
      'Трёхразовое питание',
      'Консультация и контроль врача',
      'До 5 процедур в день',
      'Лечебная гимнастика и аква-занятия',
      'Массаж или физиотерапия по назначению'
    ],
    featured: true
  },
  {
    name: 'Полный курс',
    duration: '14 ночей',
    price: 'от 137 200 ₽',
    perNight: 'от 9 800 ₽ / ночь',
    includes: [
      'Проживание в номере «Полулюкс»',
      'Трёхразовое питание',
      'Расширенная диагностическая консультация',
      'До 6 процедур в день',
      'Индивидуальный план восстановления',
      'Рекомендации после завершения программы'
    ]
  }
]

const faq = [
  {
    id: 'duration',
    question: 'Сколько длится программа?',
    answer:
      'Минимальный курс - 7 ночей. Для более заметного и устойчивого результата обычно рекомендуют 10-14 ночей; точную длительность определяет врач после консультации.'
  },
  {
    id: 'price',
    question: 'Что входит в стоимость?',
    answer:
      'В базовую стоимость входят проживание, трёхразовое питание, консультация врача и лечебные процедуры согласно выбранному курсу. Итоговая цена зависит от категории номера, дат заезда и назначенного плана.'
  },
  {
    id: 'contraindications',
    question: 'Есть ли противопоказания?',
    answer:
      'Да. Перед началом программы врач оценивает состояние гостя, анамнез и ограничения. При наличии противопоказаний часть процедур может быть заменена или не назначена.'
  },
  {
    id: 'documents',
    question: 'Какие документы нужны?',
    answer:
      'Для заезда обычно необходимы паспорт, полис ОМС и медицинские документы. Санаторно-курортную карту или выписку лучше заранее согласовать с администратором.'
  },
  {
    id: 'booking',
    question: 'Как записаться на консультацию?',
    answer:
      'Оставьте имя и телефон в форме на странице. Администратор свяжется с вами, уточнит удобные даты и ответит на вопросы по программе и проживанию.'
  }
]

const form = reactive({
  name: '',
  phone: '',
  consent: false
})

const errors = reactive({
  name: '',
  phone: '',
  consent: ''
})

const status = ref('idle')
const errorMessage = ref('')

const phoneDigits = computed(() => form.phone.replace(/\D/g, ''))

function formatPhone() {
  let digits = phoneDigits.value.slice(0, 11)

  if (!digits) {
    form.phone = ''
    return
  }

  if (digits[0] === '8') {
    digits = `7${digits.slice(1)}`
  }

  if (digits[0] !== '7') {
    digits = `7${digits}`
  }

  const parts = [
    '+7',
    digits.slice(1, 4),
    digits.slice(4, 7),
    digits.slice(7, 9),
    digits.slice(9, 11)
  ].filter(Boolean)

  form.phone = parts
    .slice(0, 1)
    .concat(parts.slice(1).map((part, index) => (index === 0 ? ` (${part}` : part)))
    .join('')

  if (digits.length > 4) form.phone += ')'
  if (digits.length > 7) form.phone += `-${digits.slice(7, 9)}`
  if (digits.length > 9) form.phone += `-${digits.slice(9, 11)}`
}

function validate() {
  errors.name = form.name.trim().length < 2 ? 'Укажите имя' : ''
  errors.phone = phoneDigits.value.length !== 11 ? 'Укажите телефон полностью' : ''
  errors.consent = form.consent ? '' : 'Нужно согласие на обработку данных'

  return !errors.name && !errors.phone && !errors.consent
}

async function submit() {
  status.value = 'idle'
  errorMessage.value = ''

  if (!validate()) {
    return
  }

  status.value = 'sending'

  try {
    const data = new FormData()

    data.append('name', form.name.trim())
    data.append('phone', form.phone)
    data.append('consent', 'yes')
    data.append('_subject', 'Заявка на консультацию: программа восстановления спины и суставов')

    const response = await fetch(FORMSPREE_ENDPOINT, {
      method: 'POST',
      body: data,
      headers: {
        Accept: 'application/json'
      }
    })

    if (!response.ok) {
      throw new Error('Заявка не отправилась. Попробуйте ещё раз или позвоните нам.')
    }

    status.value = 'success'
    form.name = ''
    form.phone = ''
    form.consent = false
  } catch (error) {
    status.value = 'error'
    errorMessage.value = error.message
  }
}

function openModal(id) {
  activeModal.value = id
}

function closeModal() {
  activeModal.value = null
}

function toggleFaq(id) {
  openFaq.value = openFaq.value === id ? null : id
}

function onScroll() {
  isScrolled.value = window.scrollY > 24
}

function onKeydown(event) {
  if (event.key === 'Escape') {
    closeModal()
  }
}

onMounted(() => {
  window.addEventListener('scroll', onScroll, { passive: true })
  window.addEventListener('keydown', onKeydown)
})

onBeforeUnmount(() => {
  window.removeEventListener('scroll', onScroll)
  window.removeEventListener('keydown', onKeydown)
})
</script>

<template>
  <header class="header" :class="{ 'header--scrolled': isScrolled }">
    <div class="container header__inner">
      <a class="header__logo" href="#top">Санаторий на озере</a>

      <nav class="header__nav" aria-label="Основная навигация">
        <a href="#program">Программа</a>
        <a href="#stay">Проживание</a>
        <a href="#faq">Вопросы</a>
      </nav>

      <a class="header__phone" href="tel:+70000000000">+7 000 000-00-00</a>
      <a class="btn btn--primary header__cta" href="#booking">Записаться</a>
    </div>
  </header>

  <main id="top">
    <section class="hero">
      <div class="container hero__grid">
        <div class="hero__content">
          <p class="eyebrow">Программа восстановления</p>

          <h1>Восстановление спины и суставов</h1>

          <ul class="tags" aria-label="Основные направления программы">
            <li>Спина</li>
            <li>Суставы</li>
            <li>Движение</li>
            <li>Отдых у озера</li>
          </ul>

          <p class="hero__text">
            Индивидуальный формат восстановления для гостей санатория. Состав процедур и длительность
            определяются после консультации специалиста.
          </p>

          <p class="hero__price">
            <strong>Цена:</strong> рассчитывается индивидуально
          </p>

          <div class="hero__actions">
            <a class="btn btn--primary" href="#booking">Записаться на консультацию</a>
            <a class="btn btn--secondary" href="#program">Смотреть программу</a>
          </div>
        </div>

        <figure class="hero__media">
          <img
            :src="heroImage"
            alt="Озеро и природа рядом с санаторием"
            width="1200"
            height="900"
            fetchpriority="high"
            decoding="async"
          />
        </figure>
      </div>
    </section>

    <section id="program" class="section">
      <div class="container">
        <div class="section__head">
          <div>
            <p class="eyebrow">Программа</p>
            <h2>Что входит в восстановление</h2>
            <p class="section__subtitle">
              Пример наполнения программы. Точный состав процедур назначается после консультации.
            </p>
          </div>
        </div>

        <div class="rail" role="list">
          <button
            v-for="item in programCards"
            :key="item.id"
            type="button"
            class="card"
            role="listitem"
            :aria-label="`Подробнее: ${item.title}`"
            @click="openModal(item.id)"
          >
            <div class="card__media">
              <img :src="item.image" alt="" width="600" height="450" loading="lazy" decoding="async" />
            </div>

            <div class="card__body">
              <h3 class="card__title">{{ item.title }}</h3>
              <p class="card__text">{{ item.text }}</p>
              <span class="card__link">Подробнее</span>
            </div>
          </button>
        </div>
      </div>
    </section>

    <section id="stay" class="section section--alt">
      <div class="container">
        <div class="section__head">
          <div>
            <p class="eyebrow">Проживание</p>
            <h2>Отдых и условия размещения</h2>
            <p class="section__subtitle">
              Пример блоков. Замените изображения и описания на актуальные данные санатория.
            </p>
          </div>
        </div>

        <div class="rail" role="list">
          <button
            v-for="item in stayCards"
            :key="item.id"
            type="button"
            class="card"
            role="listitem"
            :aria-label="`Подробнее: ${item.title}`"
            @click="openModal(item.id)"
          >
            <div class="card__media">
              <img :src="item.image" alt="" width="600" height="450" loading="lazy" decoding="async" />
            </div>

            <div class="card__body">
              <h3 class="card__title">{{ item.title }}</h3>
              <p class="card__text">{{ item.text }}</p>
              <span class="card__link">Подробнее</span>
            </div>
          </button>
        </div>
      </div>
    </section>
<section id="prices" class="section section--alt">
  <div class="container">
    <div class="section__head">
      <div>
        <p class="eyebrow">Стоимость</p>
        <h2>Программы и цены</h2>
        <p class="section__subtitle">
          Стоимость указана за человека при двухместном размещении. Итоговая цена зависит от дат
          заезда, категории номера и назначенных процедур.
        </p>
      </div>
    </div>

    <div class="pricing">
      <article
        v-for="plan in pricing"
        :key="plan.name"
        class="price-card"
        :class="{ 'price-card--featured': plan.featured }"
      >
        <p class="price-card__badge">{{ plan.duration }}</p>
        <h3>{{ plan.name }}</h3>

        <p class="price-card__price">{{ plan.price }}</p>
        <p class="price-card__per-night">{{ plan.perNight }}</p>

        <ul class="price-card__list">
          <li v-for="feature in plan.includes" :key="feature">
            {{ feature }}
          </li>
        </ul>

        <a class="btn btn--primary" href="#booking">Записаться</a>
      </article>
    </div>

    <p class="pricing-note">
      Цены являются ориентировочными и могут меняться в зависимости от сезона и наличия мест.
    </p>
  </div>
</section>
<section class="section">
  <div class="container">
    <div class="section__head">
      <div>
        <p class="eyebrow">Галерея</p>
        <h2>Атмосфера санатория</h2>
        <p class="section__subtitle">
          Озеро, прогулки, процедуры и комфортный отдых в течение программы.
        </p>
      </div>
    </div>

    <div class="gallery">
      <figure v-for="(image, key) in images" :key="key" class="gallery__item">
        <img
          :src="image"
          alt="Фотография санатория и территории у озера"
          loading="lazy"
          decoding="async"
          width="800"
          height="600"
        />
      </figure>
    </div>
  </div>
</section>
    <section id="booking" class="section">
      <div class="container">
        <div class="conversion">
          <div class="conversion__content">
            <p class="eyebrow">Запись</p>
            <h2>Записаться на консультацию</h2>
            <p class="section__subtitle">
              Оставьте имя и телефон. Администратор свяжется с вами, уточнит детали и ответит на вопросы.
            </p>
          </div>

          <form class="form" novalidate @submit.prevent="submit">
            <div class="field">
              <label for="booking-name">Имя</label>
              <input
                id="booking-name"
                v-model="form.name"
                name="name"
                type="text"
                autocomplete="name"
                required
                :aria-invalid="Boolean(errors.name)"
                aria-describedby="booking-name-error"
              />
              <p v-if="errors.name" id="booking-name-error" class="field__error">{{ errors.name }}</p>
            </div>

            <div class="field">
              <label for="booking-phone">Телефон</label>
              <input
                id="booking-phone"
                v-model="form.phone"
                name="phone"
                type="tel"
                inputmode="tel"
                autocomplete="tel"
                placeholder="+7 (000) 000-00-00"
                required
                :aria-invalid="Boolean(errors.phone)"
                aria-describedby="booking-phone-error"
                @input="formatPhone"
              />
              <p v-if="errors.phone" id="booking-phone-error" class="field__error">{{ errors.phone }}</p>
            </div>

            <label class="checkbox">
              <input v-model="form.consent" type="checkbox" name="consent" required />
              <span>
                Я согласен на обработку персональных данных для связи по заявке.
              </span>
            </label>
            <p v-if="errors.consent" class="field__error">{{ errors.consent }}</p>

            <button
              class="btn btn--primary form__submit"
              type="submit"
              :disabled="status === 'sending'"
            >
              {{ status === 'sending' ? 'Отправляем' : 'Записаться на консультацию' }}
            </button>

            <p v-if="status === 'error'" class="form-status form-status--error" role="alert">
              {{ errorMessage }}
            </p>

            <p v-if="status === 'success'" class="form-status form-status--success" role="status">
              Заявка отправлена. Мы свяжемся с вами в ближайшее время.
            </p>
          </form>
        </div>
      </div>
    </section>

    <section id="faq" class="section section--alt">
      <div class="container">
        <div class="section__head">
          <div>
            <p class="eyebrow">Вопросы</p>
            <h2>Частые вопросы</h2>
          </div>
        </div>

        <div class="accordion">
          <div v-for="item in faq" :key="item.id" class="accordion__item">
            <h3>
              <button
                class="accordion__button"
                type="button"
                :aria-expanded="openFaq === item.id"
                :aria-controls="`faq-${item.id}`"
                @click="toggleFaq(item.id)"
              >
                {{ item.question }}
                <span aria-hidden="true">{{ openFaq === item.id ? '-' : '+' }}</span>
              </button>
            </h3>

            <div
              v-show="openFaq === item.id"
              :id="`faq-${item.id}`"
              class="accordion__panel"
            >
              {{ item.answer }}
            </div>
          </div>
        </div>
      </div>
    </section>

    <section class="section">
      <div class="container">
        <div class="conversion conversion--deep">
          <div class="conversion__content">
            <p class="eyebrow">Консультация</p>
            <h2>Узнайте, подойдёт ли программа</h2>
            <p class="section__subtitle">
              Оставьте контакты, и администратор санатория свяжется с вами для уточнения деталей.
            </p>

            <a class="btn btn--primary" href="#booking">Записаться на консультацию</a>
          </div>
        </div>
      </div>
    </section>
  </main>

  <div v-if="activeModal" class="modal" @click.self="closeModal">
    <div
      class="modal__panel"
      role="dialog"
      aria-modal="true"
      :aria-labelledby="`modal-title-${activeModal}`"
    >
      <button class="modal__close" type="button" aria-label="Закрыть" @click="closeModal">×</button>

      <template v-for="item in [...programCards, ...stayCards]" :key="item.id">
        <div v-if="item.id === activeModal">
          <h3 :id="`modal-title-${item.id}`">{{ item.title }}</h3>
          <p class="modal__text">{{ item.details }}</p>
          <a class="btn btn--primary" href="#booking" @click="closeModal">
            Записаться на консультацию
          </a>
        </div>
      </template>
    </div>
  </div>

  <div class="mobile-bar">
    <a class="btn btn--secondary" href="tel:+70000000000">Позвонить</a>
    <a class="btn btn--primary" href="#booking">Записаться</a>
  </div>

  <footer class="footer">
    <div class="container footer__grid">
      <div>
        <p class="footer__title">Санаторий</p>
        <ul class="footer__list">
          <li><a href="#program">Программа</a></li>
          <li><a href="#stay">Проживание</a></li>
          <li><a href="#faq">Вопросы</a></li>
        </ul>
      </div>

      <div>
        <p class="footer__title">Контакты</p>
        <ul class="footer__list">
          <li><a href="tel:+70000000000">+7 000 000-00-00</a></li>
          <li><a href="mailto:booking@sanatorium.ru">booking@sanatorium.ru</a></li>
        </ul>
      </div>

      <div>
        <p class="footer__title">Адрес</p>
        <p class="footer__list">Адрес санатория уточняется</p>
      </div>

      <div>
        <p class="footer__title">Документы</p>
        <ul class="footer__list">
          <li><a href="#">Политика обработки данных</a></li>
          <li><a href="#">Правила проживания</a></li>
        </ul>
      </div>
    </div>
  </footer>
</template>

<style scoped>
/* ---------- Header ---------- */

.header {
  position: sticky;
  top: 0;
  z-index: 30;
  background: rgba(255, 255, 255, 0.92);
  border-bottom: 1px solid var(--line);
  backdrop-filter: blur(14px);
  transition: box-shadow 0.3s ease;
}

.header--scrolled {
  box-shadow: 0 8px 28px rgba(23, 38, 46, 0.08);
}

.header__inner {
  display: flex;
  align-items: center;
  gap: 24px;
  min-height: 76px;
  transition: min-height 0.3s var(--ease);
}

.header--scrolled .header__inner {
  min-height: 62px;
}

.header__logo {
  margin-right: auto;
  color: var(--ink);
  font-family: "Lora", Georgia, serif;
  font-size: 21px;
  text-decoration: none;
}

.header__nav {
  display: none;
  gap: 30px;
}

.header__nav a {
  position: relative;
  padding-block: 6px;
  color: var(--ink);
  font-size: 15px;
  font-weight: 500;
  text-decoration: none;
  transition: color 0.2s ease;
}

.header__nav a::after {
  content: "";
  position: absolute;
  left: 0;
  bottom: 0;
  width: 100%;
  height: 2px;
  border-radius: 2px;
  background: var(--accent);
  transform: scaleX(0);
  transform-origin: left;
  transition: transform 0.35s var(--ease);
}

.header__nav a:hover,
.header__nav a:focus-visible {
  color: var(--accent);
}

.header__nav a:hover::after,
.header__nav a:focus-visible::after {
  transform: scaleX(1);
}

.header__phone {
  display: none;
  color: var(--ink);
  font-weight: 500;
  text-decoration: none;
  transition: color 0.2s ease;
}

.header__phone:hover,
.header__phone:focus-visible {
  color: var(--accent);
}

.header__cta {
  display: none;
  min-height: 46px;
  padding: 10px 26px;
}

/* ---------- Hero ---------- */

.hero {
  position: relative;
  color: var(--deep-ink);
  background: var(--deep);
}

.hero::after {
  content: "";
  position: absolute;
  inset: 0;
  background: linear-gradient(
    90deg,
    rgba(18, 56, 70, 0.94) 0%,
    rgba(18, 56, 70, 0.6) 55%,
    rgba(18, 56, 70, 0.2) 100%
  );
  pointer-events: none;
}

.hero__grid {
  position: relative;
  z-index: 1;
  display: grid;
  gap: 32px;
  padding-block: 56px;
}

.hero__content {
  display: grid;
  align-content: center;
  gap: 20px;
  max-width: 620px;
}

.hero .eyebrow {
  background: rgba(255, 255, 255, 0.14);
  color: var(--deep-ink);
  backdrop-filter: blur(6px);
}

.hero h1 {
  color: var(--deep-ink);
}

.tags {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin: 0;
  padding: 0;
  list-style: none;
}

.tags li {
  padding: 7px 16px;
  border-radius: 999px;
  background: rgba(255, 255, 255, 0.14);
  color: var(--deep-ink);
  font-size: 13px;
  backdrop-filter: blur(6px);
}

.hero__text {
  max-width: 540px;
  color: rgba(244, 247, 247, 0.88);
  font-size: 18px;
}

.hero__price {
  font-size: 18px;
}

.hero__price strong {
  color: #e2c387;
}

.hero__actions {
  display: grid;
  gap: 12px;
  margin-top: 8px;
}

.hero .btn--secondary {
  border-color: rgba(255, 255, 255, 0.55);
  color: var(--deep-ink);
}

.hero .btn--secondary:hover,
.hero .btn--secondary:focus-visible {
  background: var(--deep-ink);
  border-color: var(--deep-ink);
  color: var(--deep);
}

.hero__media {
  margin: 0;
  overflow: hidden;
  border-radius: var(--radius-card);
  box-shadow: var(--shadow-lg);
}

.hero__media img {
  width: 100%;
  height: 100%;
  min-height: 300px;
  object-fit: cover;
  aspect-ratio: 4 / 3;
}

/* ---------- Conversion block ---------- */

.conversion {
  display: grid;
  gap: 28px;
  padding: 28px;
  border-radius: var(--radius-card);
  background: var(--bg-alt);
}

.conversion--deep {
  background: var(--deep);
  color: var(--deep-ink);
  box-shadow: var(--shadow-lg);
}

.conversion--deep h2 {
  color: var(--deep-ink);
}

.conversion--deep .eyebrow {
  background: rgba(255, 255, 255, 0.14);
  color: var(--deep-ink);
}

.conversion--deep .section__subtitle {
  color: rgba(244, 247, 247, 0.82);
}

.conversion__content {
  display: grid;
  align-content: center;
  gap: 16px;
}

/* ---------- Form ---------- */

.form {
  padding: 28px;
  border-radius: var(--radius-card);
  background: var(--surface);
  color: var(--ink);
  box-shadow: var(--shadow);
}

.form__submit {
  width: 100%;
  margin-top: 8px;
}

.modal__text {
  margin-block: 16px 24px;
  color: var(--muted);
}

/* ---------- Breakpoints ---------- */

@media (min-width: 720px) {
  .header__nav,
  .header__phone,
  .header__cta {
    display: flex;
    align-items: center;
  }

  .hero__grid {
    grid-template-columns: 1.05fr 0.95fr;
    align-items: center;
    gap: 56px;
    padding-block: 112px;
  }

  .hero__actions {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }

  .hero__media img {
    aspect-ratio: 3 / 2;
  }

  .conversion {
    grid-template-columns: 1fr 1fr;
    gap: 48px;
    padding: 52px;
  }

  .form {
    padding: 40px;
  }
}
</style>