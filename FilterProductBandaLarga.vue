<template>
  <div id="filter">
    <div class="container">
      <!-- filtro de planos (desktop) -->
      <OptionsFilterLateral :filtro="filtro" :range="range" @changeState="onChangeState" />

      <div class="container-box-desktop">
        <div class="box-filter-and-products">
          <div class="filter-found-city">153 Resultados encontrado em</div>
          <!-- ESTADO E AlTERAR ESTADO -->
          <div class="filter-state">
            <div class="state-name">
              <div class="state-img" />
              14025-590, 637
            </div>
            <div class="state-alt">Alterar</div>
          </div>
          <!-- TODOS FILTROS -->
          <div class="filter-filtros">
            <!-- FILTRO DE PLANOS MOBILE -->
            <div class="filtro-planos" @click="open_modal = true">
              <div class="filtro-planos-container">
                Filtros
                <div class="img-filter" />
              </div>
            </div>
            <!-- FILTRO DE ORDENAÇÃO -->
            <div class="filtro-ordem">
              <div class="filtro-ordem-box" @click="; (active_ordem = !active_ordem) | (active_type = false)">
                <div class="ordem-box-container">
                  <div class="ordem-title">
                    <small>Ordenado por</small>
                    {{ ordem }}
                  </div>
                  <div class="menu-arrow" :class="{ rotate_item: active_ordem }" />
                </div>
              </div>
              <transition name="slide-side">
                <div v-if="active_ordem" class="ordem-dropdown">
                  <div class="ordem-dropdown-itens" @click="ordem = 'Menor preço'">
                    <p>Menor preço</p>
                  </div>
                  <div class="ordem-dropdown-itens" @click="ordem = 'Maior preço'">
                    <p>Maior preço</p>
                  </div>
                  <div class="ordem-dropdown-itens" @click="ordem = 'Mais vendido'">
                    <p>Mais vendido</p>
                  </div>
                  <div class="ordem-dropdown-itens" @click="ordem = 'Mais procurado'">
                    <p>Mais procurado</p>
                  </div>
                </div>
              </transition>
            </div>
          </div>
        </div>
        <div class="box-cards-products">
          <ProductBandaLarga v-for="(item, index) in products3el" :key="index" :values="item" />
          <ReguaBandaLarga class="w-full" title="Não sabe qual é o plano de internet ideal para você?" />
          <ProductBandaLarga v-for="(item, index) in productsActive" :key="index" :values="item" />
        </div>
        <div class="box-plans">
          <div class="button-explore-plans">Ver mais resultado</div>
          <div class="date-updated">
            Última atualização | <span>03/02/2022</span>
          </div>
        </div>
      </div>
    </div>
    <!-- Modal filtro (MOBILE) -->
    <ModalFilter />
  </div>
</template>

<script>
import { products } from '~/static/products.json'
export default {
  name: 'FilterProducts',
  data() {
    return {
      products3el: [],
      productsActive: [],
      isMobile: true,
      open_modal: false,
      price_open_modal: false,
      range: {
        minAngle: 0,
        maxAngle: 600,
      },
      filtro: {
        'Ver Planos': {
          multiple: true,
          options: [
            {
              name: 'internet',
              active: false,
            },
            {
              name: 'Internet + streaming',
              active: false,
            },
            {
              name: 'Internet + TV',
              active: false,
            },
            {
              name: 'Internet + Fixo',
              active: false,
            },
            {
              name: 'Internet + TV + Fixo',
              active: false,
            },
          ],
        },
        Velocidade: {
          multiple: false,
          options: [
            {
              name: 'Até 100 Mega',
              active: false,
              velocity: {
                min: 0,
                max: 100
              }
            },
            {
              name: '101 Mega a 200 Mega',
              active: false,
              velocity: {
                min: 101,
                max: 200
              }
            },
            {
              name: '201 Mega a 500 Mega',
              active: false,
              velocity: {
                min: 201,
                max: 500
              }
            },
            {
              name: 'Acima de 500 Mega',
              active: false,
              velocity: {
                min: 501,
                max: 999
              }
            },
          ],
        },
        'Tipo de conexão': {
          multiple: false,
          options: [
            {
              name: 'Fibra',
              active: true,
            },
            {
              name: 'Cabo',
              active: false,
            },
          ],
        },
      },
      price: {
        min: 0,
        max: 600,
      },
      type: 'Para Residência',
      active_type: false,
      ordem: 'Menor preço',
      active_ordem: false,
    }
  },
  computed: {
    products() {
      return products
    },
  },
  mounted() {
    this.productsActive = products
    this.generateProducts3el()
  },

  methods: {
    generateProducts3el() {
      const size = this.productsActive.length > 3 ? 3 : this.productsActive.length
      console.log(this.productsActive)
      for (let i = 0; i < size; i++) {
        this.products3el.push(this.productsActive.shift())
      }
    },
    onChangeState({ group, index }) {
      if (!this.filtro[group].multiple) {
        this.filtro[group].options.forEach(
          element => (element.active = false),
        )

      }
      this.filtro[group].options[index].active =
        !this.filtro[group].options[index].active
      console.log(products)
      switch (group) {
        case "Velocidade":
          this.productsActive = products.filter(
            item => item.velocity >= this.filtro[group].options[index].velocity.min &&
              item.velocity <= this.filtro[group].options[index].velocity.max)
          this.generateProducts3el()
          break;

        default:
          break;
      }
      console.log(this.filtro[group].options[index].active)
    },
  },
}
</script>

<style lang="scss" scoped>
// MOBILE
#filter {
  padding: 25px 0;

  .container {
    .filter-planos-desktop {
      display: none;
    }

    .filter-state {
      display: flex;
      justify-content: space-between;
      align-items: center;
      border-bottom: 2px solid #cccccc;
      padding-bottom: 15px;

      .state-name {
        font-size: 16px;
        font-weight: 600;
        display: flex;
        align-items: center;

        .state-img {
          background: url('~/assets/img/location-logo.svg') no-repeat;
          background-size: 100%;
          width: 18px;
          height: 24px;
          margin-right: 15px;
          margin-bottom: 2px;
        }
      }

      .state-alt {
        color: #1523ff;
        font-size: 13px;
        font-weight: 600;
        text-decoration: underline;
      }
    }

    .filter-filtros {
      display: flex;
      flex-wrap: wrap;

      .filter-title {
        font-size: 13px;
        font-weight: 600;
      }

      .filtro-type {
        width: 100%;
        position: relative;

        .filtro-type-box {
          width: -webkit-fill-available;
          height: 50px;
          border: 2px solid #cccccc;
          border-radius: 5px;
          font-size: 14px;
          font-weight: 600;
          display: flex;
          align-items: center;
          justify-content: center;

          .filtro-type-container {
            width: 90%;
            display: flex;
            align-items: center;
            justify-content: space-between;
          }
        }

        .type-dropdown {
          position: absolute;
          top: 52px;
          background-color: white;
          border: 2px solid #cccccc;
          border-radius: 5px;
          width: -webkit-fill-available;
          z-index: 2;
          cursor: pointer;

          .type-dropdown-itens {
            display: flex;
            flex-direction: column;

            align-items: center;

            &:hover {
              background-color: rgb(245, 245, 245);
            }

            p {
              font-size: 13px;
              font-weight: 600;
              width: 90%;
            }
          }
        }
      }

      .filtro-planos {
        margin-top: 3%;
        width: 39%;
        height: 50px;
        border: 2px solid #cccccc;
        font-size: 14px;
        border-radius: 5px;
        font-weight: 600;
        display: flex;
        align-items: center;
        justify-content: center;
        margin-right: 3%;

        .filtro-planos-container {
          width: 80%;
          display: flex;
          align-items: center;
          justify-content: space-between;

          .img-filter {
            background: url('~/assets/img/img-logo.svg') no-repeat;
            background-size: 100%;
            width: 16px;
            height: 13px;
          }
        }
      }

      // FILTRO ORDEM + DROPDOWN
      .filtro-ordem {
        position: relative;
        margin-top: 3%;
        flex: 1;

        .filtro-ordem-box {
          height: 50px;
          border: 2px solid #cccccc;
          border-radius: 5px;

          display: flex;
          align-items: center;
          justify-content: center;

          .ordem-box-container {
            width: 80%;
            display: flex;
            align-items: center;
            justify-content: space-between;

            .ordem-title {
              font-size: 13px;
              font-weight: 600;
              max-width: 105px;

              small {
                font-size: 10px;
                font-weight: 500;
              }
            }
          }
        }

        //ORDEM DROPDOWN
        .ordem-dropdown {
          top: 52px;
          position: absolute;
          background-color: white;
          border: 2px solid #cccccc;
          border-radius: 5px;
          width: -webkit-fill-available;
          z-index: 2;
          cursor: pointer;

          .ordem-dropdown-itens {
            padding: 3px 0;
            display: flex;
            justify-content: center;
            cursor: pointer;

            &:hover {
              background-color: rgb(245, 245, 245);
            }

            p {
              font-size: 13px;
              font-weight: 600;
              width: 80%;
            }
          }
        }
      }
    }

    .box-cards-products {
      width: 100%;
      display: flex;
      flex-wrap: wrap;
      align-items: baseline;
      justify-content: space-around;
    }

    .box-plans {
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      align-items: center;

      .date-updated {
        display: flex;
        text-align: end;

        span {
          font-weight: 600;
        }
      }

      .button-explore-plans {
        width: 100%;
        max-width: 280px;
        height: 50px;
        background-color: #1523ff;
        border-radius: 5px;
        color: white;
        font-size: 13px;
        font-weight: 600;
        display: flex;
        justify-content: center;
        align-items: center;
        margin: 30px 0;
        cursor: pointer;

        &:hover {
          background-color: #0d1ceb;
          transition: all 0.1s ease-in;
          transform: scale(1.02);
        }
      }
    }
  }
}

@media (min-width: 1024px) {
  #filter {
    .container {
      .box-cards-products {
        justify-content: space-between;
      }
    }
  }
}

// DESKTOP
@media (min-width: 1280px) {
  #filter {
    .container {
      display: flex;
      align-items: flex-start;
      justify-content: space-between;

      .filter-planos-desktop {
        width: 293px;
        height: auto;
        border: 2px solid #cccccc;
        border-radius: 5px;
        display: flex;
        flex-direction: column;
        align-items: center;

        .planos-container {
          width: 80%;
          padding: 40px 0;

          .filter-title-desk {
            font-size: 20px;
            font-weight: 600;
          }

          .box-list-desk {
            margin-top: 50px;
          }
        }
      }

      .container-box-desktop {
        width: 72%;

        .box-filter-and-products {
          display: flex;
          justify-content: space-between;

          .filter-found-city {
            display: flex;
            align-items: center;
            font-size: 1rem;
            width: 700px;
          }

          .filter-state {
            width: 700px;
            border: none;
            padding-bottom: 0;

            .state-name {
              font-size: 20px;

              .state-img {
                width: 20px;
                height: 27px;
              }
            }

            .state-alt {
              font-size: 16px;
            }
          }

          .filter-filtros {
            display: flex;
            justify-content: flex-end;
            width: 100%;

            .filter-title {
              display: none;
            }

            .filtro-type {
              max-width: 216px;
              margin-top: 0;
              cursor: pointer;

              .filtro-type-box {
                height: 62px;

                .filtro-type-container {
                  width: 85%;
                }
              }

              .type-dropdown {
                top: 64px;

                p {
                  width: 85%;
                }
              }
            }

            .filtro-planos {
              display: none;
            }

            .filtro-ordem {
              max-width: 321px;
              margin-top: 0;
              cursor: pointer;
              margin-left: 50px;

              .filtro-ordem-box {
                height: 62px;

                .ordem-box-container {
                  width: 85%;

                  .ordem-title {
                    max-width: none;
                    font-size: 16px;

                    small {
                      font-size: 16px;
                    }
                  }
                }
              }

              .ordem-dropdown {
                top: 64px;
              }
            }
          }
        }
      }

      .box-cards-products {
        margin-top: 30px;
      }

      .button-explore-plans {
        margin: 28px 0 0 0;
        height: 65px;
        font-size: 16px;

        .button-arrow {
          width: 23px;
          height: 11px;
        }
      }

      .box-plans {
        flex-direction: row;

        .button-explore-plans {
          font-size: 16px;
        }
      }
    }
  }
}
</style>
