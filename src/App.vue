<template>
  <main>
    <header class="position-relative">
      <img class="bg desk" src="/images/bg-header-desktop.svg" alt="">
      <img class="bg mobile" src="/images/bg-header-mobile.svg" alt="">
      <div class="filter-cont container d-flex align-items-center p-3 px-4 px-md-5 rounded-1 position-absolute gap-3"
        v-if="filters.length > 0">
        <div class="filters flex-fill d-flex aling-items-center flex-wrap gap-4">
          <div class="item d-flex align-items-center justify-content-between rounded" v-for="(item, index) in filters"
            :key="index">
            <span class="fw-bold p-1 px-2">{{ item }}</span>
            <div class="w-100 h-100 rounded-end p-1 px-2" @click="removeFilter(item)">
              <img src="/images/icon-remove.svg" alt="">
            </div>
          </div>
        </div>
        <span class="clear fw-bold position-relative" @click="clearFilter">Clear</span>
      </div>
    </header>
    <div class="companies pb-5 px-3 px-md-0 container">
      <div :class="`company bg-white rounded-1 p-4 ${company.featured ? 'featured' : ''}`"
        v-for="(company, index) in filteration" :key="company.id">
        <div class="info d-flex align-items-center justify-content-between flex-column flex-md-row gap-lg-0 gap-4 ">
          <div class="d-flex aling-items-center gap-4">
            <img :src="`./images/${company.logo}`" alt="">
            <div class="details">
              <div class="head d-flex align-items-center gap-3">
                <span class="name fw-bold">{{ company.company }}</span>
                <span class="new rounded-pill text-light px-3 text-uppercase" v-if="company.new">New!</span>
                <span class="featured rounded-pill text-light px-3 text-uppercase" v-if="company.featured">featured</span>
              </div>
              <h4 class="job my-2 fw-bold">{{ company.position }}</h4>
              <div class="others">
                <span class="date">{{ company.postedAt }}</span>
                <span class="work-hours mx-5">{{ company.contract }}</span>
                <span class="work-type">{{ company.location }}</span>
              </div>
            </div>
          </div>
          <div class="experiences d-flex align-items-start gap-3 flex-wrap">
            <span :class="`role d-block p-1 px-3 fw-bold rounded-1 ${filters.includes(company.role) ? 'active' : ''}`"
              data-info="role" :data-tag="company.role" @click="makingFilter($event, company.role)">{{ company.role
              }}</span>
            <span :class="`level d-block p-1 px-3 fw-bold rounded-1 ${filters.includes(company.level) ? 'active' : ''}`"
              data-info="level" :data-tag="company.level" @click="makingFilter($event, company.level)">{{ company.level
              }}</span>
            <div class="langs" v-for="(lang, index) in company.languages" :key="index">
              <span :class="`lang d-block p-1 px-3 fw-bold rounded-1 ${filters.includes(lang) ? 'active' : ''}`"
                data-info="lang" :data-tag="lang" @click="makingFilter($event, lang)">{{ lang }}</span>
            </div>
            <div class="tools" v-for="(tool, index) in company.tools" :key="index">
              <span :class="`tool d-block p-1 px-3 fw-bold rounded-1 ${filters.includes(tool) ? 'active' : ''}`"
                data-info="tool" :data-tag="tool" @click="makingFilter($event, tool)">{{ tool }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>
<script setup>
import { ref, computed, onMounted } from 'vue'

const companies = ref([])

const filters = ref([])

const set = ref(new Set())


let filteration = computed(() => {
  return companies.value.filter(el => {
    const tags = [el.role, el.level].concat(el.languages, el.tools)
    return filters.value.every(filter => tags.includes(filter))
  })
})


const makingFilter = (event, companyInfo) => {
  if (event.target.dataset.info === 'role') {
    set.value.add(companyInfo)
  } else if (event.target.dataset.info === 'level') {
    set.value.add(companyInfo)
  } else if (event.target.dataset.info === 'lang') {
    set.value.add(companyInfo)
  } else if (event.target.dataset.info === 'tool') {
    set.value.add(companyInfo)
  }
  filters.value = [...set.value]
}


const clearFilter = () => {
  set.value.clear()
  filters.value = []
}

const removeFilter = (item) => {
  filters.value = filters.value.filter(el => el != item)
  set.value.delete(item)
}


onMounted(async () => {
  await fetch('data.json').then(response => response.json()).then(data => {
    companies.value = data
  })
})

</script>
<style lang="scss">
* {
  box-sizing: border-box;
}

:root {
  // ### Primary
  --Desaturated-Dark-Cyan: hsl(180, 29%, 50%);

  // ### Neutral
  --Light-Grayish-Cyan-Background: hsl(180, 52%, 96%);
  --Light-Grayish-Cyan-Filter-Tablets: hsl(180, 31%, 95%);
  --Dark-Grayish-Cyan: hsl(180, 8%, 52%);
  --Very-Dark-Grayish-Cyan: hsl(180, 14%, 20%);
}

body {
  font-size: 15px;
  font-family: 'League Spartan', sans-serif;
  background-color: var(--Light-Grayish-Cyan-Background);
}

ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.mobile {
  display: none;
}

main {
  header {
    width: 100%;
    height: 100%;
    background-color: var(--Desaturated-Dark-Cyan);

    .bg {
      width: 100%;
    }

    .filter-cont {
      background-color: white;
      left: 50%;
      transform: translateX(-50%);
      bottom: -27px;
      box-shadow: 0 5px 20px rgb(174 188 189 / 64%);

      .filters {
        .item {

          width: fit-content;
          background-color: var(--Light-Grayish-Cyan-Filter-Tablets);

          span {
            color: var(--Desaturated-Dark-Cyan);
          }

          &>div {
            background-color: var(--Desaturated-Dark-Cyan);
            transition: 0.2s;
            cursor: pointer;

            &:hover {
              background-color: var(--Very-Dark-Grayish-Cyan);
            }

            img {
              user-select: none;
              pointer-events: none;
              width: 12px;
            }
          }
        }
      }

      .clear {
        color: var(--Desaturated-Dark-Cyan);
        cursor: pointer;

        &::before {
          content: '';
          position: absolute;
          width: 0;
          left: 50%;
          transition: 0.2s;
          transform: translateX(-50%);
          bottom: 0;
          height: 2px;
          background-color: var(--Desaturated-Dark-Cyan);
          border-radius: 50px;
        }

        &:hover::before {
          width: 100%;
        }
      }
    }
  }

  .companies {
    margin-top: 60px;

    .company {
      box-shadow: 0 5px 20px rgba(164, 198, 201, 0.747);

      &.featured {
        border-left: 5px solid var(--Desaturated-Dark-Cyan);
      }

      &:not(:last-of-type) {
        margin-bottom: 25px;
      }

      .info {
        .details {
          .head {
            .name {
              color: var(--Desaturated-Dark-Cyan);
              font-size: 18px;
            }

            .new,
            .featured {
              padding-top: 4px;
              padding-bottom: 1px;
            }

            .new {
              background-color: var(--Desaturated-Dark-Cyan);
            }

            .featured {
              background-color: var(--Very-Dark-Grayish-Cyan);
            }
          }

          .job {
            color: var(--Very-Dark-Grayish-Cyan);
          }

          .others {
            span {
              color: var(--Dark-Grayish-Cyan);
              position: relative;

              &:nth-child(2)::before,
              &:nth-child(2)::after {
                content: '';
                position: absolute;
                width: 4px;
                height: 4px;
                border-radius: 50%;
                background-color: var(--Dark-Grayish-Cyan);
                top: 50%;
                transform: translateY(-50%);
              }

              &:nth-child(2)::before {
                left: -26px;
              }

              &:nth-child(2)::after {
                right: -26px;
              }
            }
          }
        }
      }

      .experiences {
        span {
          cursor: pointer;
          color: var(--Desaturated-Dark-Cyan);
          background-color: var(--Light-Grayish-Cyan-Filter-Tablets);
          transition: 0.2s;

          &.active {
            background-color: var(--Desaturated-Dark-Cyan);
            color: white;
          }

          &:hover {
            background-color: var(--Desaturated-Dark-Cyan);
            color: white;
          }
        }
      }
    }
  }

  @media (max-width: 991px) {
    .mobile {
      display: block;
    }

    .desk {
      display: none;
    }

    .filter-cont {
      width: calc(100% - 32px);
    }

    .companies {
      margin-top: 100px;

      .company {
        margin-bottom: 50px !important;

        .info {
          position: relative;
          flex-direction: column !important;

          &>div {
            width: 100%;
          }

          &>div:nth-child(1) {
            margin-top: 30px;
            padding-bottom: 24px;
            border-bottom: 1px solid var(--Dark-Grayish-Cyan);
          }

          img {
            position: absolute;
            top: -55px;
            width: 68px;
          }
        }
      }
    }
  }
}</style>
