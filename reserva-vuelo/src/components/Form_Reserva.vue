<template>
  <div class="background">
    <div class="container my-5 form-container">
      <h2 class="text-center mb-4">Reserva de Vuelo</h2>
      <form @submit.prevent="submitForm" class="p-4 border rounded shadow-sm bg-light">
        <!-- Datos del Pasajero -->
        <div class="form-section mb-4">
          <h4>Datos del Pasajero</h4>
          <div class="row">
            <div class="col-md-6 mb-3">
              <label for="fullName" class="form-label">Nombre Completo</label>
              <input 
                v-model="form.fullName" 
                @input="validateFullName" 
                type="text" 
                id="fullName" 
                class="form-control"
                :class="{ 'is-invalid': errors.fullName }"
              />
              <div v-if="errors.fullName" class="invalid-feedback">{{ errors.fullName }}</div>
            </div>
            <div class="col-md-6 mb-3">
              <label for="passportNumber" class="form-label">Número de Pasaporte</label>
              <input 
                v-model="form.passportNumber" 
                @input="validatePassportNumber" 
                type="text" 
                id="passportNumber" 
                class="form-control"
                :class="{ 'is-invalid': errors.passportNumber }"
              />
              <div v-if="errors.passportNumber" class="invalid-feedback">{{ errors.passportNumber }}</div>
            </div>
          </div>
          <div class="row">
            <div class="col-md-6 mb-3">
              <label for="birthDate" class="form-label">Fecha de Nacimiento</label>
              <input 
                v-model="form.birthDate" 
                @input="validateBirthDate" 
                type="date" 
                id="birthDate" 
                class="form-control"
                :class="{ 'is-invalid': errors.birthDate }"
              />
              <div v-if="errors.birthDate" class="invalid-feedback">{{ errors.birthDate }}</div>
            </div>
            <div class="col-md-6 mb-3">
              <label for="nationality" class="form-label">Nacionalidad</label>
              <select v-model="form.nationality" class="form-select">
                <option value="">Selecciona tu país</option>
                <option v-for="country in countries" :key="country" :value="country">{{ country }}</option>
              </select>
            </div>
          </div>
        </div>
  
        <!-- Datos del Vuelo -->
        <div class="form-section mb-4">
          <h4>Datos del Vuelo</h4>
          <div class="row">
            <div class="col-md-6 mb-3">
              <label for="departureCity" class="form-label">Ciudad de Origen</label>
              <select v-model="form.departureCity" @change="validateCity" class="form-select" :class="{ 'is-invalid': errors.departureCity }">
                <option value="">Selecciona una ciudad</option>
                <option v-for="city in airports" :key="city" :value="city">{{ city }}</option>
              </select>
              <div v-if="errors.departureCity" class="invalid-feedback">{{ errors.departureCity }}</div>
            </div>
            <div class="col-md-6 mb-3">
              <label for="destinationCity" class="form-label">Ciudad de Destino</label>
              <select v-model="form.destinationCity" @change="validateCity" class="form-select" :class="{ 'is-invalid': errors.destinationCity }">
                <option value="">Selecciona una ciudad</option>
                <option v-for="city in airports" :key="city" :value="city">{{ city }}</option>
              </select>
              <div v-if="errors.destinationCity" class="invalid-feedback">{{ errors.destinationCity }}</div>
            </div>
          </div>
          <div class="row">
            <div class="col-md-6 mb-3">
              <label for="departureDate" class="form-label">Fecha de Salida</label>
              <input 
                v-model="form.departureDate" 
                @input="validateDepartureDate" 
                type="date" 
                id="departureDate" 
                class="form-control"
                :class="{ 'is-invalid': errors.departureDate }"
              />
              <div v-if="errors.departureDate" class="invalid-feedback">{{ errors.departureDate }}</div>
            </div>
            <div class="col-md-6 mb-3">
              <label for="returnDate" class="form-label">Fecha de Regreso (opcional)</label>
              <input 
                v-model="form.returnDate" 
                @input="validateReturnDate" 
                type="date" 
                id="returnDate" 
                class="form-control"
                :class="{ 'is-invalid': errors.returnDate }"
              />
              <div v-if="errors.returnDate" class="invalid-feedback">{{ errors.returnDate }}</div>
            </div>
          </div>
          <div class="row">
            <div class="col-md-6 mb-3">
              <label for="flightClass" class="form-label">Clase de Vuelo</label>
              <select v-model="form.flightClass" class="form-select">
                <option value="">Selecciona la clase</option>
                <option value="Economica">Económica</option>
                <option value="Ejecutiva">Ejecutiva</option>
                <option value="Primera Clase">Primera Clase</option>
              </select>
              <div v-if="form.flightClass" class="mt-2">
                <strong>Monto:</strong> {{ flightCost }}
              </div>
            </div>
            <div class="col-md-6 mb-3">
              <label for="ticketCount" class="form-label">Número de Boletos</label>
              <input 
                v-model="form.ticketCount" 
                @input="validateTicketCount" 
                type="number" 
                id="ticketCount" 
                min="1" 
                max="10" 
                class="form-control"
                :class="{ 'is-invalid': errors.ticketCount }"
              />
              <div v-if="errors.ticketCount" class="invalid-feedback">{{ errors.ticketCount }}</div>
              <p><strong>Total a pagar:</strong> {{ totalCost }}</p>
            </div>
          </div>
        </div>
        <!-- Datos de Pago -->
        <div class="form-section mb-4">
          <h4>Datos de Pago</h4>
          <div class="row">
            <div class="col-md-6 mb-3">
              <label for="tarjeta" class="form-label">Número de Tarjeta</label>
              <input 
                v-model="localData.tarjeta" 
                @input="handleCardInput" 
                type="text" 
                id="tarjeta" 
                class="form-control"
                :class="{ 'is-invalid': errors.tarjeta }"
                maxlength="16"
              />
              <div v-if="errors.tarjeta" class="invalid-feedback">{{ errors.tarjeta }}</div>
              <img v-if="cardImage" :src="cardImage" alt="Card" class="mt-2 img-fluid" style="max-width: 80px;" />
            </div>
            <div class="col-md-6 mb-3">
              <label for="cardName" class="form-label">Nombre en la Tarjeta</label>
              <input 
                v-model="form.cardName" 
                @input="validateCardName" 
                type="text" 
                id="cardName" 
                class="form-control"
                :class="{ 'is-invalid': errors.cardName }"
              />
              <div v-if="errors.cardName" class="invalid-feedback">{{ errors.cardName }}</div>
            </div>
          </div>
          <div class="row">
            <div class="col-md-6 mb-3">
              <label for="expiryDate" class="form-label">Fecha de Vencimiento</label>
              <input 
                v-model="form.expiryDate" 
                @input="validateExpiryDate" 
                type="text" 
                id="expiryDate" 
                placeholder="MM/AA" 
                class="form-control"
                :class="{ 'is-invalid': errors.expiryDate }"
              />
              <div v-if="errors.expiryDate" class="invalid-feedback">{{ errors.expiryDate }}</div>
            </div>
            <div class="col-md-6 mb-3">
              <label for="cvv" class="form-label">CVV</label>
              <input 
                v-model="form.cvv" 
                @input="validateCVV" 
                type="text" 
                id="cvv" 
                maxlength="4"
                class="form-control"
                :class="{ 'is-invalid': errors.cvv }"
              />
              <div v-if="errors.cvv" class="invalid-feedback">{{ errors.cvv }}</div>
            </div>
          </div>
        </div>
        <button type="submit" class="btn btn-primary w-100" :disabled="!isFormValid">Reservar</button>
      </form>

      <!-- Modal de Resumen de Reserva -->
      <div v-if="showSummary" class="modal fade show" tabindex="-1" style="display: block;">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title">Resumen de Reserva</h5>
              <button type="button" class="btn-close" @click="showSummary = false"></button>
            </div>
            <div class="modal-body">
              <p><strong>Nombre Completo:</strong> {{ form.fullName }}</p>
              <p><strong>Número de Pasaporte:</strong> {{ form.passportNumber }}</p>
              <p><strong>Fecha de Nacimiento:</strong> {{ form.birthDate }}</p>
              <p><strong>Nacionalidad:</strong> {{ form.nationality }}</p>
              <p><strong>Ciudad de Origen:</strong> {{ form.departureCity }}</p>
              <p><strong>Ciudad de Destino:</strong> {{ form.destinationCity }}</p>
              <p><strong>Fecha de Salida:</strong> {{ form.departureDate }}</p>
              <p><strong>Fecha de Regreso:</strong> {{ form.returnDate }}</p>
              <p><strong>Clase de Vuelo:</strong> {{ form.flightClass }}</p>
              <p><strong>Número de Boletos:</strong> {{ form.ticketCount }}</p>
              <p><strong>Nombre en la Tarjeta:</strong> {{ form.cardName }}</p>
              <p><strong>Fecha de Vencimiento:</strong> {{ form.expiryDate }}</p>
              <p><strong>CVV:</strong> {{ form.cvv }}</p>
              <p><strong>Monto Total:</strong> {{ totalCost }}</p>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" @click="showSummary = false">Cerrar</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import airportsData from '../assets/airports.json';
import countriesData from '../assets/countries.json';

export default {
  data() {
    return {
      form: {
        fullName: '',
        passportNumber: '',
        birthDate: '',
        nationality: '',
        departureCity: '',
        destinationCity: '',
        departureDate: '',
        returnDate: '',
        flightClass: '',
        ticketCount: 1,
        cardNumber: '',
        expiryDate: '',
        cvv: '',
        cardName: '',
        tarjeta: ''
      },
      localData: {
        tarjeta: ''
      },
      cardImage: null,
      errors: {},
      airports: airportsData.map(a => a.city),
      countries: countriesData.map(c => c.nameES),
      showSummary: false
    };
  },
  computed: {
    isFormValid() {
      return Object.values(this.errors).every(error => !error);
    },
    flightCost() {
      switch (this.form.flightClass) {
        case 'Economica':
          return 250000;
        case 'Ejecutiva':
          return 450000;
        case 'Primera Clase':
          return 800000;
        default:
          return 0;
      }
    },
    totalCost() {
      const costPerTicket = this.flightCost;
      return costPerTicket * this.form.ticketCount;
    }
  },
  methods: {
    validateFullName() {
      this.errors.fullName = /^[a-zA-Z\s]{3,}$/.test(this.form.fullName) ? null : 'Debe tener al menos 3 letras y solo contener letras y espacios.';
    },
    validatePassportNumber() {
      this.errors.passportNumber = /^[A-Z0-9]{6,9}$/.test(this.form.passportNumber) ? null : 'Debe contener entre 6 y 9 caracteres alfanuméricos.';
    },
    validateBirthDate() {
      const age = new Date().getFullYear() - new Date(this.form.birthDate).getFullYear();
      this.errors.birthDate = age >= 18 ? null : 'Debe ser mayor de 18 años.';
    },
    validateCity() {
      if (this.form.departureCity && this.form.destinationCity && this.form.departureCity === this.form.destinationCity) {
        this.errors.departureCity = 'La ciudad de origen y destino deben ser diferentes.';
        this.errors.destinationCity = 'La ciudad de origen y destino deben ser diferentes.';
      } else {
        this.errors.departureCity = null;
        this.errors.destinationCity = null;
      }
    },
    validateDepartureDate() {
      const today = new Date();
      this.errors.departureDate = new Date(this.form.departureDate) > today ? null : 'La fecha debe ser posterior a la actual.';
    },
    validateReturnDate() {
      this.errors.returnDate = new Date(this.form.returnDate) > new Date(this.form.departureDate) ? null : 'La fecha de regreso debe ser posterior a la de salida.';
    },
    validateTicketCount() {
      this.errors.ticketCount = this.form.ticketCount >= 1 && this.form.ticketCount <= 10 ? null : 'Debe elegir entre 1 y 10 boletos.';
    },
    handleCardInput() {
      this.localData.tarjeta = this.localData.tarjeta.slice(0, 16);
      this.validateTarjeta();
      this.detectCardType();
    },
    validateTarjeta() {
      const regex = /^[0-9]{16}$/;
      if (!regex.test(this.localData.tarjeta)) {
        this.errors.tarjeta = "Número de tarjeta inválido.";
      } else {
        delete this.errors.tarjeta;
      }
    },
    detectCardType() {
      const tarjeta = this.localData.tarjeta;
      if (/^4/.test(tarjeta)) {
        this.cardImage = new URL('../assets/images/visa.png', import.meta.url).href;
      } else if (/^5[1-5]/.test(tarjeta)) {
        this.cardImage = new URL('../assets/images/mastercard.png', import.meta.url).href;
      } else if (/^3[47]/.test(tarjeta)) {
        this.cardImage = new URL('../assets/images/amex.png', import.meta.url).href;
      } else {
        this.cardImage = null;
      }
    },
    validateExpiryDate() {
      const [month, year] = this.form.expiryDate.split('/');
      const now = new Date();
      this.errors.expiryDate = month >= 1 && month <= 12 && (parseInt(year) > now.getFullYear() % 100) ? null : 'Formato MM/AA y en el futuro.';
    },
    validateCVV() {
      this.errors.cvv = /^\d{3,4}$/.test(this.form.cvv) ? null : 'El CVV debe tener 3 o 4 dígitos.';
    },
    validateCardName() {
      this.errors.cardName = /^[a-zA-Z\s]+$/.test(this.form.cardName) ? null : 'Solo letras y espacios.';
    },
    submitForm() {
      this.showSummary = true;
    }
  }
};
</script>

<style>
html, body {
  height: 100%;
}

.background {
  background-image: url('../assets/images/avion3.png'); 
  background-size: cover;
  background-position: center;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.form-container {
  background-color: rgb(240 240 240 / 71%); 
  padding: 20px;
  border-radius: 10px;
}

.bg-light {
    --bs-bg-opacity: 1;
    background-color: rgb(248 249 250 / 14%) !important;
}

.invalid-feedback {
  color: red;
}
</style>