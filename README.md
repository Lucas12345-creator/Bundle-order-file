<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Data Bundle Order</title>
  <link href="https://fonts.googleapis.com/css2?family=Roboto&display=swap" rel="stylesheet">
  <style>
    body {
      font-family: 'Roboto', sans-serif;
      background-color: #f6f8fc;
      margin: 0;
      padding: 2rem;
    }
    .container {
      max-width: 600px;
      margin: auto;
      background: white;
      padding: 2rem;
      border-radius: 12px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.1);
    }
    h2 {
      text-align: center;
      margin-bottom: 1rem;
      color: #333;
    }
    label {
      display: block;
      margin-top: 1rem;
      font-weight: 500;
    }
    input, select, textarea {
      width: 100%;
      padding: 0.75rem;
      margin-top: 0.5rem;
      border: 1px solid #ccc;
      border-radius: 8px;
      font-size: 1rem;
    }
    button {
      margin-top: 1.5rem;
      width: 100%;
      padding: 0.75rem;
      font-size: 1rem;
      background-color: #4285f4;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
    }
    button:hover {
      background-color: #3367d6;
    }
    .hidden {
      display: none;
    }
    .success-message {
      text-align: center;
      color: green;
      margin-top: 1rem;
    }
    .summary {
      margin-top: 1rem;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <div class="container">
    <h2>Bundle Order Form</h2>
    <form id="orderForm">
      <label>Your Name (will be used as password)</label>
      <input type="text" name="password" required>

      <label>Network</label>
      <select name="network" required>
        <option value="">Select Network</option>
        <option value="MTN">MTN</option>
        <option value="Telecel">Telecel</option>
        <option value="AirtelTigo">AirtelTigo</option>
      </select>

      <div class="beneficiary-block">
        <label>Beneficiary 1</label>
        <input type="tel" name="beneficiary1" required>

        <label>Bundle 1 (GB)</label>
        <input type="number" name="bundle1_gb" min="1" required>

        <input type="hidden" name="amount1">
      </div>

      <div class="beneficiary-block hidden" id="beneficiary2-block">
        <label>Beneficiary 2</label>
        <input type="tel" name="beneficiary2">

        <label>Bundle 2 (GB)</label>
        <input type="number" name="bundle2_gb" min="1">

        <input type="hidden" name="amount2">
      </div>

      <div class="beneficiary-block hidden" id="beneficiary3-block">
        <label>Beneficiary 3</label>
        <input type="tel" name="beneficiary3">

        <label>Bundle 3 (GB)</label>
        <input type="number" name="bundle3_gb" min="1">

        <input type="hidden" name="amount3">
      </div>

      <div class="beneficiary-block hidden" id="beneficiary4-block">
        <label>Beneficiary 4</label>
        <input type="tel" name="beneficiary4">

        <label>Bundle 4 (GB)</label>
        <input type="number" name="bundle4_gb" min="1">

        <input type="hidden" name="amount4">
      </div>

      <div class="beneficiary-block hidden" id="beneficiary5-block">
        <label>Beneficiary 5</label>
        <input type="tel" name="beneficiary5">

        <label>Bundle 5 (GB)</label>
        <input type="number" name="bundle5_gb" min="1">

        <input type="hidden" name="amount5">
      </div>

      <button type="button" id="addMore">+ Add Another Beneficiary</button>

      <div class="summary" id="summary"></div>

      <input type="hidden" name="status" value="Pending">
      <button type="submit">Submit Order</button>
    </form>
    <div class="success-message" id="successMessage"></div>
  </div>

  <script>
    const form = document.getElementById('orderForm');
    const addMoreBtn = document.getElementById('addMore');
    const summary = document.getElementById('summary');
    let visibleBeneficiaries = 1;

    addMoreBtn.addEventListener('click', () => {
      if (visibleBeneficiaries >= 5) return;
      visibleBeneficiaries++;
      document.getElementById(`beneficiary${visibleBeneficiaries}-block`).classList.remove('hidden');
    });

    form.addEventListener('submit', async (e) => {
      e.preventDefault();

      const formData = new FormData(form);
      let total = 0;

      for (let i = 1; i <= 5; i++) {
        const bundle = parseInt(formData.get(`bundle${i}_gb`) || 0);
        const amount = bundle * 6; // GH¢6 per GB
        formData.set(`amount${i}`, bundle ? amount : '');
        total += amount;
      }

      formData.set('status', 'Pending');
      summary.textContent = `Total Amount: GH¢${total}`;

      try {
        await fetch('https://script.google.com/macros/s/AKfycbyknNGc_hWBpvPGCDTSOEsRskELUvIZ4bzk9RTm4KVr1Ra3k-W7brzSMdubw_Avhtus/exec', {
          method: 'POST',
          mode: 'no-cors',
          body: new URLSearchParams(formData)
        });
        document.getElementById('successMessage').textContent = 'Order submitted successfully!';
        form.reset();
        summary.textContent = '';
        visibleBeneficiaries = 1;
        document.querySelectorAll('.beneficiary-block').forEach((el, i) => {
          if (i > 0) el.classList.add('hidden');
        });
      } catch (error) {
        alert('There was an error submitting your order.');
        console.error(error);
      }
    });
  </script>
</body>
</html>
