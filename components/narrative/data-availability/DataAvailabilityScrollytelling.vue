<template>
  <section class="bg-dark">
    <div class="container p-5 scrollytelling">
      <div class="scrollytelling__graphics">
        <div ref="developmentGoals" class="goals-container">
          <div
            v-for="goal in $goals"
            :key="goal.id"
            ref="goal"
            :style="{ backgroundColor: goal.color }"
            class="d-flex justify-content-center align-items-center goal-circle"
          >
            <img
              :src="
                require(`~/assets/images/sdg-icons/sdg_icon-${goal.id}.svg`)
              "
              :alt="goal.title + '| UDNP'"
            />
          </div>
        </div>
        <div
          ref="dataAvailabilityAxis"
          class="fs-7 text-light data-availability-axis"
        >
          <div class="data-availability-axis__arrow"></div>
          <div class="data-availability-axis__labels">
            <span>0%</span>
            <span>DATA AVAILABILITY</span>
            <span>100%</span>
          </div>
        </div>
      </div>

      <Scrollama
        class="text-light scrollytelling__texts"
        :offset="0.5"
        @step-enter="stepEnterHandler"
        @step-exit="stepExitHandler"
      >
        <div class="step">
          <p class="fs-4 mb-5">
            The 2030 Agenda for Sustainable Development, adopted by all United
            Nations Member States in 2015, provides a shared blueprint for peace
            and prosperity for people and the planet.
          </p>
          <p class="fs-4 mb-5">
            At its heart are the 17 Sustainable Development Goals (SDGs), which
            are integrated, indivisible and balance the three dimensions of
            sustainable development: the economic, social and environmental.
          </p>
          <p class="fs-4 mb-0">
            Each SDG is tracked and monitored with a set of indicators that are
            common across countries according to the global indicator framework.
            Methodology and standards to collect the data for all indicators are
            now available.
          </p>
        </div>

        <div class="step">
          <p class="fs-4 mb-5">
            Tracking progress towards implementing the 17 SDGs should go hand in
            hand with measuring the statistical capacity of countries. Data
            availability at the indicator level helps us understand which
            dimensions of the three economic, social and environmental are least
            reported on, and which communities are left out when reporting on
            the efforts to implement the SDGs at the national, regional and
            global levels.
          </p>
        </div>

        <div class="step">
          <p class="fs-4 mb-0">
            Some countries don’t collect enough data about the environment and
            the sustainable development of their cities and/r specific
            communities.
          </p>
        </div>

        <div class="step">
          <p class="fs-4 mb-0">
            Other countries fail in reporting on social inequalities in rural
            areas and the gender disparity across many levels.
          </p>
        </div>
      </Scrollama>
    </div>
  </section>
</template>

<script>
import goalsPositions from '~/data/data-availability-scrollytelling-positions.json'

export default {
  name: 'DataAvailabilityScrollytellingComponent',

  data() {
    return {
      goalsPositions: null,
      goalsCirclePositions: [],
      isGraphicAlreadyEntered: false,
    }
  },

  beforeMount() {
    this.goalsPositions = goalsPositions
  },

  mounted() {
    const deg = 0
    const radius = 200
    const fields = this.$refs.goal
    const width = 500
    const height = 500
    let angle = deg || Math.PI * 3.5
    const step = (2 * Math.PI) / fields.length

    fields.forEach((field, index) => {
      const x = Math.round(
        width / 2 + radius * Math.cos(angle) - field.offsetWidth / 2
      )
      const y = Math.round(
        height / 2 + radius * Math.sin(angle) - field.offsetHeight / 2
      )

      const position = {
        id: this.$goals[index].id,
        x: (x * 100) / width + '%',
        y: (y * 100) / height + '%',
      }

      this.goalsCirclePositions.push(position)

      angle += step
    })
  },

  methods: {
    stepEnterHandler({ element, index, direction }) {
      element.classList.add('current-step')

      if (direction === 'down') {
        switch (index) {
          case 0:
            this.animateStepA()
            break
          case 1:
            this.animateStepB()
            break
          case 2:
            this.animateStepC()
            break
          case 3:
            this.animateStepD()
            break
          default:
            break
        }
      }
    },

    stepExitHandler({ element, index, direction }) {
      element.classList.remove('current-step')

      if (direction === 'up') {
        switch (index) {
          case 0:
            break
          case 1:
            this.animateStepBReversed()
            break
          case 2:
            this.animateStepCReversed()
            break
          case 3:
            this.animateStepC()
            break
          default:
            break
        }
      }
    },

    clearAnimations() {
      this.$anime.running.forEach((animation) => {
        animation?.pause()
      })
    },

    animateStepA() {
      this.clearAnimations()

      const fields = this.$refs.goal

      const tl = this.$anime.timeline()

      fields.forEach((field, index) => {
        const circlePosition = this.goalsCirclePositions[index]

        tl.add(
          {
            targets: field,
            opacity: 1,
            left: circlePosition?.x,
            top: circlePosition?.y,
            translateX: 0,
            translateY: 0,
            easing: 'easeInOutQuint',
            duration: 800,
          },
          0
        )
      })
    },

    animateStepB() {
      this.clearAnimations()

      const fields = this.$refs.goal

      const tl = this.$anime.timeline()

      tl.add(
        {
          targets: document.querySelectorAll('.goal-circle img'),
          opacity: 0,
          easing: 'easeOutQuint',
          duration: 1000,
        },
        0
      )

      fields.forEach((field, index) => {
        tl.add(
          {
            targets: field,
            opacity: 1,
            top: '40%',
            left: this.goalsPositions[index].step1 + '%',
            translateX: '-50%',
            easing: 'easeOutQuint',
            duration: 1000,
          },
          0
        )
      })

      tl.add(
        {
          targets: this.$refs.dataAvailabilityAxis,
          opacity: 0.3,
          easing: 'easeOutQuint',
          duration: 1200,
        },
        0
      )
    },

    animateStepC() {
      this.clearAnimations()

      const fields = this.$refs.goal

      const tl = this.$anime.timeline()

      tl.add(
        {
          targets: document.querySelectorAll('.goal-circle img'),
          opacity: 0,
          easing: 'easeOutQuint',
          duration: 1000,
        },
        0
      )

      fields.forEach((field, index) => {
        tl.add(
          {
            targets: field,
            opacity: 1,
            top: '40%',
            left: this.goalsPositions[index].step2 + '%',
            translateX: '-50%',
            easing: 'easeOutQuint',
            duration: 1500,
          },
          0
        )
      })

      tl.add(
        {
          targets: this.$refs.dataAvailabilityAxis,
          opacity: 0.3,
          easing: 'easeOutQuint',
          duration: 1200,
        },
        0
      )
    },

    animateStepD() {
      this.clearAnimations()

      const fields = this.$refs.goal

      const tl = this.$anime.timeline()

      tl.add(
        {
          targets: document.querySelectorAll('.goal-circle img'),
          opacity: 0,
          easing: 'easeOutQuint',
          duration: 1000,
        },
        0
      )

      fields.forEach((field, index) => {
        tl.add(
          {
            targets: field,
            opacity: 1,
            top: '40%',
            left: this.goalsPositions[index].step3 + '%',
            translateX: '-50%',
            easing: 'easeOutQuint',
            duration: 1500,
          },
          0
        )
      })

      tl.add(
        {
          targets: this.$refs.dataAvailabilityAxis,
          opacity: 0.3,
          easing: 'easeOutQuint',
          duration: 1200,
        },
        0
      )
    },

    animateStepBReversed() {
      this.clearAnimations()

      const fields = this.$refs.goal

      const tl = this.$anime.timeline()

      tl.add(
        {
          targets: this.$refs.dataAvailabilityAxis,
          opacity: 0,
          easing: 'easeOutQuint',
          duration: 1200,
        },
        0
      )

      fields.forEach((field, index) => {
        const circlePosition = this.goalsCirclePositions[index]

        tl.add(
          {
            targets: field,
            opacity: 1,
            left: circlePosition?.x,
            top: circlePosition?.y,
            translateX: 0,
            translateY: 0,
            easing: 'easeInOutQuint',
            duration: 1000,
          },
          0
        )
      })

      tl.add(
        {
          targets: document.querySelectorAll('.goal-circle img'),
          opacity: 1,
          easing: 'easeInOutQuint',
          duration: 1000,
        },
        0
      )
    },

    animateStepCReversed() {
      this.clearAnimations()

      const fields = this.$refs.goal

      const tl = this.$anime.timeline()

      fields.forEach((field, index) => {
        tl.add(
          {
            targets: field,
            left: this.goalsPositions[index].step1 + '%',
            easing: 'easeOutQuint',
            duration: 1500,
          },
          0
        )
      })
    },
  },
}
</script>

<style lang="scss" scoped>
.goals-container {
  position: relative;
  height: 100%;
}

.goal-circle {
  width: 50px;
  height: 50px;
  border-radius: 50%;

  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  opacity: 0;

  & img {
    width: 100%;
    height: auto;
  }
}

.data-availability-axis {
  display: flex;
  flex-direction: column;
  position: absolute;
  width: 95%;
  top: 55%;
  left: 50%;
  transform: translateX(-50%);
  opacity: 0;
}

.data-availability-axis__arrow {
  // LINE
  height: 1px;
  background: white;
  width: 100%;
  margin: 0 auto;
  margin-bottom: 10px;
  position: relative;

  // This max width is due to the min and max position of the goals circles
  // on the axis (80% => 400px) plus half of the width of a single goal circle
  // on both sides (25px + 25px)
  max-width: 450px;

  // ARROW
  &::after {
    content: '';
    border: solid white;
    border-width: 0 1px 1px 0;
    display: inline-block;
    padding: 3px;
    transform: rotate(-45deg);
    position: absolute;
    right: 0;
    top: -3px;
  }
}

.data-availability-axis__labels {
  display: grid;
  grid-template-columns: 30px 1fr 30px;
  grid-gap: 10px;

  & span:nth-child(2) {
    justify-self: center;
  }

  & span:nth-child(3) {
    justify-self: flex-end;
  }
}
</style>
