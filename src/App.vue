<template>
  <div id="app">
    <HeaderTello></HeaderTello>
    <div class="board">
      <div class="lane" v-for="(content, key) in cards" :key="key">
        <h2 class="lane-title">{{ key }}</h2>
        <Container
          group-name="cardstello"
          @drag-start="handleDragStart(key, $event)"
          @drop="handleDrop(key, $event)"
          :get-child-payload="getChildPayload"
          :drop-placeholder="{ className: 'placeholder' }"
        >
          <Draggable v-for="card in content" :key="card.id">
            <CardTello>{{ card.text }}</CardTello>
          </Draggable>
        </Container>
      </div>
    </div>
  </div>
</template>

<script>
import HeaderTello from './components/HeaderTello.vue'
import CardTello from './components/CardTello.vue'
import { Container, Draggable } from 'vue-smooth-dnd'

import initialCards from './mocks/initials_cards'

export default {
  name: 'App',
  components: {
    HeaderTello,
    CardTello,
    Container,
    Draggable
  },
  async created () {
    // const response = 
    const response = await fetch('http://localhost:5001/smg/api/vuelta/dialaboral/5d533c469ccaad0a04447557/7-7-2023', {
        headers: {
        'Authorization': `Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiI1ZDg5NzY0MzZkMjgxZDRlYzMwNTk1N2EiLCJyb2wiOnsiY29kaWdvIjoiOTkifSwiaWF0IjoxNjkyNjU3MDc5fQ.EoMlDI48fbtmFtP22J6OFju0wYlA0B5ETp3nB5ud6rM`
        }
    });
    const data = await response.json();
    console.log(data);
    
  },
  data () {
    return {
      cards: {...initialCards},
      draggindCard: {
        lane: '',
        index: -1,
        cardData: {},
      }
    }
  },
  methods: {
    handleDragStart(keyCard, dragResult) {
      const { payload, isSource } = dragResult
      if (isSource) {
        this.draggindCard = {
          keyCard,
          index: payload.index,
          cardData: {
            ...this.cards[keyCard][payload.index]
          }
        }
      }
    },
    handleDrop(keyCard, dropResult) {
      const { removedIndex, addedIndex } = dropResult
      if (keyCard === this.draggindCard.keyCard && removedIndex === addedIndex) {
        return
      }

      if (removedIndex !== null) {
        this.cards[keyCard].splice(removedIndex, 1)
      }

      if (addedIndex !== null) {
        this.cards[keyCard].splice(addedIndex, 0, this.draggindCard.cardData)
      }
    },
    getChildPayload(index) {
      return { index }
    }
  }
}
</script>

<style>
.board {
  display: flex;
  justify-content: flex-start;
  margin: 1.2rem 0.8rem;
  align-items: flex-start;
}

.lane {
  background: var(--color-grey);
  width: 23rem;
  border-radius: 0.8rem;
  box-shadow: 0 .1rem .2rem 0 rgba(33, 33, 33, 0.1);
  margin: 0 0.8rem;
  padding: 0 0.7rem;
}

.lane-title {
  padding: .8rem .5rem;
  margin-bottom: .6rem;
}

.placeholder {
  background: rgba(33, 33, 33, .1);
  border-radius: .4rem;
  transform: scaleY(0.85);
  transform-origin: 0% 0%;
}
</style>


<!-- 
  const jornadas = [

  {

    jornada: {},

    vueltas: [{nvuelta: 1}, {nvuelta: 2}]

  },

  {

    jornada: {},

    vueltas: [{nvuelta: 1}, {nvuelta: 2}, {nvuelta: 3}, {nvuelta: 4}]

  },

  {

    jornada: {},

    vueltas: [{nvuelta: 1}, {nvuelta: 2}, {nvuelta: 3}]

  }

]

 

// Vueltas juntas

const vueltas = jornadas.map(jornada => jornada.vueltas).flat()

 

// Obtener numeros de vueltas

const nvueltas = vueltas.map(vuelta => vuelta.nvuelta)

const nvueltasUnicos = [...new Set(nvueltas)]

 

console.log(nvueltasUnicos)

for (const nvueltaUnico of nvueltasUnicos) {

    console.log(vueltas.filter(vuelta => vuelta.nvuelta === nvueltaUnico))

} -->