<script setup>
import { ref } from 'vue'

const name = ref('')
const wishlistItems = ref([
  { item: '', rating: 5 },
  { item: '', rating: 5 },
  { item: '', rating: 5 },
])
const isSubmitting = ref(false)
const submitStatus = ref(null) // 'success', 'error', or null

// Add a new wishlist item field
const addItem = () => {
  if (wishlistItems.value.length < 10) {
    wishlistItems.value.push({ item: '', rating: 5 })
  }
}

// Remove a wishlist item field
const removeItem = (index) => {
  if (wishlistItems.value.length > 1) {
    wishlistItems.value.splice(index, 1)
  }
}

// Get kid-friendly rating label
const getRatingLabel = (rating) => {
  if (rating <= 4) return "🍌🍌 Yeah, I'd like this"
  if (rating <= 7) return '🍌🍌🍌 Really want this!'
  if (rating <= 9) return '🍌🍌 REALLY REALLY want this! 🍌🍌'
  return '🍌🍌🍌 I HAVE TO HAVE THIS THING!! 🍌🍌🍌'
}

// Submit form to Formspree
const submitWishlist = async () => {
  if (!name.value.trim()) {
    alert('Please enter your name!')
    return
  }

  const filledItems = wishlistItems.value
    .filter((item) => item.item.trim())
    .map((item) => `${item.item} (Rating: ${item.rating}/10)`)
  if (filledItems.length === 0) {
    alert('Please add at least one item to your wishlist!')
    return
  }

  isSubmitting.value = true
  submitStatus.value = null

  try {
    const formspreeEndpoint = 'https://formspree.io/f/xldqnyjz'

    const response = await fetch(formspreeEndpoint, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        name: name.value,
        wishlist: filledItems,
        submittedAt: new Date().toISOString(),
      }),
    })

    if (response.ok) {
      submitStatus.value = 'success'
      // Reset form
      name.value = ''
      wishlistItems.value = [
        { item: '', rating: 5 },
        { item: '', rating: 5 },
        { item: '', rating: 5 },
      ]
    } else {
      submitStatus.value = 'error'
    }
  } catch (error) {
    console.error('Submission error:', error)
    submitStatus.value = 'error'
  } finally {
    isSubmitting.value = false
  }
}
</script>

<template>
  <div class="wishlist-form">
    <div class="form-header">
      <img class="smithmas-logo" src="../assets/big_santa_banana.png" alt="Smithmas Banana Logo" />
      <h1>Smithmas Wishlist</h1>
    </div>
    <p class="altmans">Hey!...The Altmans want some ideas of what you want for Christmas</p>

    <form @submit.prevent="submitWishlist" class="form">
      <div class="form-group">
        <label class="which-smith">Which Smith are you? *</label>
        <div class="radio-group">
          <label class="radio-option">
            <input type="radio" v-model="name" value="Oliver" required />
            <span>Oliver</span>
          </label>
          <label class="radio-option">
            <input type="radio" v-model="name" value="Henry" required />
            <span>Henry</span>
          </label>
        </div>
      </div>

      <div class="wishlist-section">
        <h3>Add item descriptions or website URLs below</h3>
        <div v-for="(wishItem, index) in wishlistItems" :key="index" class="wishlist-item">
          <div class="item-content">
            <input
              v-model="wishlistItems[index].item"
              type="text"
              :placeholder="`Item ${index + 1}`"
              class="item-input"
            />
            <div class="rating-control">
              <label class="rating-label">How much do you want it?</label>
              <div class="slider-container">
                <span class="slider-label-left">🍌 Meh</span>
                <input
                  type="range"
                  v-model="wishlistItems[index].rating"
                  min="1"
                  max="10"
                  class="rating-slider"
                />
                <span class="slider-label-right">🍌🍌🍌🍌 NEED IT!</span>
              </div>
              <div class="rating-message">{{ getRatingLabel(wishlistItems[index].rating) }}</div>
            </div>
          </div>
          <button
            type="button"
            @click="removeItem(index)"
            class="remove-btn"
            :disabled="wishlistItems.length === 1"
          >
            ✕
          </button>
        </div>

        <button
          type="button"
          @click="addItem"
          class="add-btn"
          :disabled="wishlistItems.length >= 10"
        >
          + Add Another Item
        </button>
      </div>

      <button type="submit" class="submit-btn" :disabled="isSubmitting">
        {{ isSubmitting ? 'Sending...' : 'Submit Wishlist' }}
      </button>

      <div v-if="submitStatus === 'success'" class="success-message">
        ✅ Your wishlist has been sent! Thank you!
      </div>

      <div v-if="submitStatus === 'error'" class="error-message">
        ❌ Oops! Something went wrong. Please try again.
      </div>
    </form>
    <p class="fine-print">
      <sup>*</sup>Submissions will be accepted until December 15th, 2025. Extreme interest in an
      item does not insure receipt. Item prices will factor into the quantity of gifts. Limit 10
      items per submission. Multiple submissions encouraged. See stores for details. jk.
    </p>
  </div>
</template>

<style scoped>
.wishlist-form {
  max-width: 600px;
  margin: 0 auto;
  padding: 2rem;
}

.form-header {
  display: flex;
  justify-content: center;
  margin-bottom: 2rem;
}

.smithmas-logo {
  display: block;
  margin: 0 20px 1rem;
  width: 60px;
  height: auto;
  animation: waggle 10s ease-in-out infinite;
}

@keyframes waggle {
  0%,
  95% {
    transform: rotate(0deg);
  }
  96% {
    transform: rotate(-15deg);
  }
  97% {
    transform: rotate(15deg);
  }
  98% {
    transform: rotate(-10deg);
  }
  99% {
    transform: rotate(10deg);
  }
  100% {
    transform: rotate(0deg);
  }
}

.form-header h1 {
  color: #c41e3a;
  font-size: 2.3rem;
  font-weight: 400;
  margin-bottom: 0.5rem;
}

.form-header p {
  color: #666;
  font-size: 1.1rem;
}

.which-smith {
  font-weight: 600;
  font-size: 1.4rem;
  color: #333;
}

.form {
  background: white;
  padding: 2rem;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
  color: #333;
  font-weight: 600;
}

.form-group input {
  width: 100%;
  padding: 0.75rem;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  font-size: 1rem;
  transition: border-color 0.3s;
}

.form-group input:focus {
  outline: none;
  border-color: #c41e3a;
}

.radio-group {
  display: flex;
  gap: 1.5rem;
  margin-top: 1rem;
}

.radio-option {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  cursor: pointer;
  font-size: 1.1rem;
  font-weight: 500;
}

.radio-option input[type='radio'] {
  width: 20px;
  height: 20px;
  cursor: pointer;
  accent-color: #c41e3a;
}

.radio-option:hover span {
  color: #c41e3a;
}

.wishlist-section {
  margin: 2rem 0;
}

.wishlist-section h3 {
  font-size: 1rem;
  font-weight: 600;
  color: #165b33;
  margin-bottom: 1rem;
}

.wishlist-item {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1.5rem;
  align-items: flex-start;
}

.item-content {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
  padding: 1rem;
  background: #f9f9f9;
  border: 2px solid #e8e8e8;
  border-radius: 12px;
  transition: all 0.3s;
}

.item-content:hover {
  background: #f5f5f5;
  border-color: #d0d0d0;
}

.item-input {
  width: 100%;
  padding: 0.75rem;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  font-size: 1rem;
  transition: border-color 0.3s;
}

.item-input:focus {
  outline: none;
  border-color: #165b33;
}

.rating-control {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.rating-label {
  font-size: 0.9rem;
  color: #666;
  font-weight: 500;
}

.slider-container {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.slider-label-left,
.slider-label-right {
  font-size: 0.85rem;
  font-weight: 600;
  color: #666;
  white-space: nowrap;
}

.rating-slider {
  flex: 1;
  height: 8px;
  border-radius: 4px;
  background: linear-gradient(to left, #ffd93d 50%, #6bcf7f 100%);
  outline: none;
  cursor: pointer;
  -webkit-appearance: none;
}

.rating-slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: #7d797dff;
  cursor: pointer;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.rating-slider::-moz-range-thumb {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: #c41e3a;
  cursor: pointer;
  border: none;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.rating-message {
  font-weight: 500;
  color: #3b3839ff;
  font-size: 1.1rem;
  text-align: center;
  padding: 0.5rem;
  background: #fff3f3;
  border-radius: 8px;
  border: 2px solid #ffe0e0;
}

.remove-btn {
  padding: 0.5rem 0.5rem;
  margin-top: 0.3rem;
  background: #c51313ff;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1rem;
  transition: background-color 0.3s;
}

.remove-btn:hover:not(:disabled) {
  background: #9b1830;
}

.remove-btn:disabled {
  background: #ccc;
  cursor: not-allowed;
}

.add-btn {
  width: 100%;
  padding: 0.75rem;
  background: #165b33;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1rem;
  font-weight: 600;
  transition: background-color 0.3s;
}

.add-btn:hover:not(:disabled) {
  background: #0e3d22;
}

.add-btn:disabled {
  background: #ccc;
  cursor: not-allowed;
}

.submit-btn {
  width: 100%;
  padding: 1rem;
  background: #c41e3a;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1.2rem;
  font-weight: 700;
  margin-top: 1.5rem;
  transition: background-color 0.3s;
}

.submit-btn:hover:not(:disabled) {
  background: 9b1830#;
}

.submit-btn:disabled {
  background: #ccc;
  cursor: not-allowed;
}

.success-message,
.error-message {
  margin-top: 1rem;
  padding: 1rem;
  border-radius: 8px;
  text-align: center;
  font-weight: 600;
}

.success-message {
  background: #d4edda;
  color: #155724;
}

.error-message {
  background: #f8d7da;
  color: #721c24;
}

.fine-print,
.altmans {
  margin-top: 1.5rem;
  font-size: 0.85rem;
  color: #666;
  text-align: center;
}

/* Mobile Responsive Styles */
@media (max-width: 640px) {
  .wishlist-form {
    padding: 0.2rem;
  }

  .form-header {
    flex-direction: column;
    align-items: center;
  }

  .smithmas-logo {
    margin: 0 0 1rem 0;
    width: 50px;
  }

  .form-header h1 {
    font-size: 1.8rem;
  }

  .which-smith {
    font-size: 1.2rem;
  }

  .form {
    padding: 1.5rem;
  }

  .wishlist-item {
    flex-direction: column;
    gap: 0.5rem;
  }

  .item-content {
    width: 100%;
    padding: 0.75rem;
  }

  .remove-btn {
    align-self: stretch;
    padding: 0.75rem;
    font-size: 0.9rem;
    margin-top: 0;
  }

  .radio-group {
    flex-direction: column;
    gap: 1rem;
  }

  .radio-option {
    font-size: 1.2rem;
  }

  .slider-label-left,
  .slider-label-right {
    font-size: 0.75rem;
  }

  .rating-message {
    font-size: 0.95rem;
    padding: 0.4rem;
  }

  .submit-btn {
    font-size: 1.1rem;
  }
}

@media (max-width: 400px) {
  .form-header h1 {
    font-size: 1.5rem;
  }

  .slider-label-left,
  .slider-label-right {
    font-size: 0.7rem;
  }

  .item-content {
    padding: 0.75rem;
  }

  .rating-message {
    font-size: 0.85rem;
  }
}
</style>
