<script setup>
import { ref } from 'vue'

const name = ref('')
const wishlistItems = ref(['', '', ''])
const email = ref('')
const isSubmitting = ref(false)
const submitStatus = ref(null) // 'success', 'error', or null

// Add a new wishlist item field
const addItem = () => {
  if (wishlistItems.value.length < 10) {
    wishlistItems.value.push('')
  }
}

// Remove a wishlist item field
const removeItem = (index) => {
  if (wishlistItems.value.length > 1) {
    wishlistItems.value.splice(index, 1)
  }
}

// Submit form to Formspree
const submitWishlist = async () => {
  if (!name.value.trim()) {
    alert('Please enter your name!')
    return
  }

  const filledItems = wishlistItems.value.filter((item) => item.trim())
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
        email: email.value,
        wishlist: filledItems,
        submittedAt: new Date().toISOString(),
      }),
    })

    if (response.ok) {
      submitStatus.value = 'success'
      // Reset form
      name.value = ''
      email.value = ''
      wishlistItems.value = ['', '', '']
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
    <img class="smithmas-logo" src="../assets/big_santa_banana.png" alt="Smithmas Banana Logo">
      <h1>Smithmas Wishlist</h1>
      <p></p>
    </div>

    <form @submit.prevent="submitWishlist" class="form">
      <div class="form-group">
        <label for="name">Your Name *</label>
        <input id="name" v-model="name" type="text" placeholder="Enter your name" required />
      </div>

      <div class="form-group">
        <label for="email">Email (optional)</label>
        <input id="email" v-model="email" type="email" placeholder="your.email@example.com" />
      </div>

      <div class="wishlist-section">
        <h3>My Wishlist</h3>
        <div v-for="(item, index) in wishlistItems" :key="index" class="wishlist-item">
          <input
            v-model="wishlistItems[index]"
            type="text"
            :placeholder="`Item ${index + 1}`"
            class="item-input"
          />
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
        {{ isSubmitting ? 'Sending...' : 'Submit Wishlist 🎅' }}
      </button>

      <div v-if="submitStatus === 'success'" class="success-message">
        ✅ Your wishlist has been sent! Thank you!
      </div>

      <div v-if="submitStatus === 'error'" class="error-message">
        ❌ Oops! Something went wrong. Please try again.
      </div>
    </form>
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
  0%, 95% {
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
  font-size: 2.5rem;
  margin-bottom: 0.5rem;
}

.form-header p {
  color: #666;
  font-size: 1.1rem;
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

.wishlist-section {
  margin: 2rem 0;
}

.wishlist-section h3 {
  color: #165b33;
  margin-bottom: 1rem;
}

.wishlist-item {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 0.75rem;
}

.item-input {
  flex: 1;
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

.remove-btn {
  padding: 0.5rem 1rem;
  background: #ff4444;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1.2rem;
  transition: background-color 0.3s;
}

.remove-btn:hover:not(:disabled) {
  background: #cc0000;
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
  background: #9b1830;
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
</style>
