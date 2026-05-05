const CircuitBreaker = require('opossum');
const axios = require('axios');

// Function to call service
async function callUserService() {
  const response = await axios.get('http://localhost:3001/users');
  return response.data;
}

// Circuit breaker options
const options = {
  timeout: 3000, // fail if request takes > 3s
  errorThresholdPercentage: 50, // % failures before opening
  resetTimeout: 5000 // time before retry
};

// Create breaker
const breaker = new CircuitBreaker(callUserService, options);

// Fallback (very important)
breaker.fallback(() => {
  return { message: 'User service unavailable. Try later.' };
});

module.exports = breaker;