<template>
  <button class="cart-btn" v-if="params" :disabled="buttonDisabled || params.id === null"
    @click="updateCartAndHandleResponse">
    <slot></slot>
    <message-node :class="messageClasses" @showMessage="message.current = message.list.default"
      :show="message.current.name">
      <template v-if="message.current.name === 'added'">
        Товар добавлен.<button @click="$router.push({ name: 'Checkout' })" class="message-link"> Оформить
          заказ?</button>
      </template>
      <template v-else-if="message.current.name === 'allError'">
        Произошла ошибка при выполнении операции...
      </template>
      <template v-else-if="message.current.name === 'sizeWrong'">
        Необходимо выбрать размер.
      </template>
    </message-node>
  </button>
</template>

<script>
import { mapState, mapGetters, mapMutations, mapActions } from "vuex";
import { cloneDeep } from 'lodash-es'

export default {
  props: {
    params: Object,
    route_base: String,
  },
  data() {
    return {
      buttonDisabled: false,
      reqiredParams: false,
      message: {
        current: {
          name: '',
          type: ''
        },
        list: {
          default: {
            name: '',
            type: ''
          },
          added: {
            name: 'added',
            type: 'success'
          },
          sizeWrong: {
            name: 'sizeWrong',
            type: 'error'
          },
          allError: {
            name: 'allError',
            type: 'error'
          }
        },
      },
    };
  },
  computed: {
    ...mapGetters({}),
    ...mapState({}),
    messageClasses() {
      return {
        'icon-check': this.message.current.type === 'success',
        'error': this.message.current.type === 'error'
      }
    }
  },
  methods: {
    ...mapMutations({}),
    ...mapActions({
      updateCart: "cart/updateCart",
    }),
    /**
     * (!) При обработке params.variations мутируют до пустого массива
     * 
     */
    async updateCartAndHandleResponse() {
      switch (this.route_base) {
        case 'cart/add-item':
          // 
          const params = cloneDeep(this.params)
          params.variations = []
          //
          let valid = this.validationVariations()
          if (valid === false) {
            this.message.current = this.message.list.sizeWrong
            return;
          }
          break;

        case 'cart/remove-item':
          if (!this.params.hasOwnProperty('key')) {
            throw 'Need cart-item key for delete'
          }
          break;

        case 'cart/update-item':
          if (!this.params.hasOwnProperty('key') && this.params.hasOwnProperty('quantity')) {
            throw 'Need cart-item params.key and/or params.quantity for update'
          }
          break;

        default:
          throw 'Попытка выполнить несуществующий или неожиданный (не применяемый) маршрут'
      }

      this.buttonDisabled = true;
      const requested = await this.updateCart({
        route_base: this.route_base,
        config: { params: this.params },
      });
      this.buttonDisabled = false;
      if (requested === undefined) this.message.current = this.message.list.allError

      switch (this.route_base) {
        case 'cart/add-item':
          this.message.current = this.message.list.added
          break;

        default:
          break;
      }
    },
    /**
     * Валидатор params.variations (в проекте единственным обязательным выборным
     * атрибутом является размер)
     */
    validationVariations() {
      let valid = true;
      for (let index = 0; index < this.params.variations.length; index++) {
        const element = this.params.variations[index];
        if (element.value) {
          continue;
        } else {
          valid = false;
          break;
        }
      }
      return valid;
    },
  },
};
</script>

<style lang="scss">
.cart-btn {
  transition: .3s;

  &_iconable {
    font-size: 2.5rem;

    &:disabled {
      color: #b9b9b9;
    }
  }

  &_textabele {
    padding: 0 30px;
    font-size: 1.4rem;
    background-color: #231f20;
    color: #fff;
    border-color: transparent;

    &:disabled {
      background-color: #584d50;
    }
  }

  .disabled {
    // background-color: #231f20;
  }


}

.message-link {
  color: #5073a2;
  margin-left: 4px;
}
</style>
