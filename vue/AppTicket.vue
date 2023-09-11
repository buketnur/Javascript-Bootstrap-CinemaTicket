<template>
    <div class="container">
      <div class="col-12 col-lg-6 mx-auto p-5 bg-dark " style="margin-top: 5rem; border-radius: 4rem;">
      <div class="screen w-50 mx-auto"></div>
      <div class="row">
        <div class="col-lg-7 col-10 mx-auto text-center mt-3">
            <div
          class="seat"
          v-for="(seat, index) in seats"
          :class="{ selected: seat.selected, reserved: seat.reserved }"
          @click="toggleSeat(index)"
          :key="index"
        ></div>
        </div>
      </div>
<div class="row">
    <div class="col-lg-6 col-10 mx-auto text-center">
        <div class="movie-list">
        <select id="movie" v-model="selectedMovieIndex" @change="calculateTotal">
          <option disabled>Film Seçiniz</option>
          <option v-for="(movie, index) in movies" :value="index" :key="index">{{ movie.name }}</option>
        </select>
      </div>
      <ul class="info">
        <li>
          <div class="seat selected"></div>
          <small>Seçili</small>
        </li>
        <li>
          <div class="seat"></div>
          <small>Boş</small>
        </li>
        <li>
          <div class="seat reserved"></div>
          <small>Dolu</small>
        </li>
      </ul>
      <p class="text">
        <span id="count">{{ selectedSeats.length }}</span> adet koltuk için hesaplanan ücret <span id="amount">{{ totalAmount }}</span> TL.
      </p>
    </div>
</div>
    </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        seats:  [],
        movies: [
          { name: "movie 1", price: 100 },
          { name: "movie 2", price: 125 },
          { name: "movie 3", price: 150 },
        ],
        selectedMovieIndex: null,
      };
    },
    computed: {
      selectedSeats() {
        return this.seats.filter((seat) => seat.selected);
      },
      totalAmount() {
        return this.selectedSeats.length * (this.selectedMovieIndex !== null ? this.movies[this.selectedMovieIndex].price : 0);

      },
      totalSeatCount() {
      return this.seats.length;
      },
    },
    methods: {
      toggleSeat(index) {
        if (!this.seats[index].reserved) {
          this.seats[index].selected = !this.seats[index].selected;
          this.calculateTotal();
          this.saveToLocalStorage();
        }
      },
      calculateTotal() {
        this.saveToLocalStorage();
      },
      getFromLocalStorage() {
        const selectedSeats = JSON.parse(localStorage.getItem("selectedSeats"));
        if (selectedSeats !== null && selectedSeats.length > 0) {
          selectedSeats.forEach((index) => {
            this.seats[index].selected = true;
          });
        }
        const selectedMovieIndex = localStorage.getItem("selectedMovieIndex");
        if (selectedMovieIndex !== null) {
          this.selectedMovieIndex = parseInt(selectedMovieIndex);
        }
      },
      saveToLocalStorage() {
        const selectedSeatIndexs = this.seats
          .map((seat, index) => (seat.selected && !seat.reserved ? index : -1))
          .filter((index) => index !== -1);
        localStorage.setItem("selectedSeats", JSON.stringify(selectedSeatIndexs));
        localStorage.setItem("selectedMovieIndex", this.selectedMovieIndex);
      },
    },
    created() {
      this.seats = Array.from({ length: 78 }, () => ({ selected: false, reserved: Math.random() < 0.2 }));
      this.getFromLocalStorage();
    },
  };
  </script>
  
  <style scoped>
body {
    background-color: #292929;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    margin: 0;
    color: #fff;
}

.seat {
    display: inline-block;
    background-color: #444451;
    height: 15px;
    width: 15px;
    margin: 3px;
    border-radius: 5px;
}

.seat.selected {
    background-color: #f6eb6f;
}

.seat.reserved {
    background-color: #fff;
    height: 15px;
    width: 15px;
    margin: 3px;
}

.seat:not(.reserved):hover {
    cursor: pointer;
    transform: scale(1.3);
}

/* .seat:nth-of-type(2) {
    margin-right: 20px;
}

.seat:nth-last-of-type(3) {
    margin-right: 20px;
} */

.row {
    display: flex;
}

.screen {
    background-color: #fff;
    height: 50px;
    width: 100%;
    margin: 20px 0;
    box-shadow: 0 3px 8px rgba(255, 255, 255, 0.7);
}

.movie-list {
    margin: 20px 0;
}

.info {
    background-color: rgba(0, 0, 0, 0.2);
    padding: 5px 10px;
    color: #777;
    display: flex;
}

.info li {
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 0 10px;
}

p.text span {
    color: #f6eb6f;
}
.text{
    color: #fff;
}
  </style>
  