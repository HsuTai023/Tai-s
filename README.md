<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <title>天空藍潛水費用</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    body { font-family: 'Segoe UI', sans-serif; padding: 20px; max-width: 500px; margin: auto; background: #e6f7ff; color: #333; }
    h1 { font-size: 1.8em; text-align: center; margin-bottom: 20px; color: #005580; }
    label, input, .equipment { margin: 10px 0; display: block; width: 100%; }
    input[type=number] {
      padding: 10px;
      border-radius: 8px;
      border: 1px solid #ccc;
      font-size: 1em;
      box-sizing: border-box;
    }
    .equipment label {
      display: flex;
      justify-content: space-between;
      align-items: center;
      background: #fff;
      padding: 10px;
      border-radius: 10px;
      margin: 5px 0;
      box-shadow: 0 2px 6px rgba(0,0,0,0.1);
      font-size: 0.95em;
    }
    .result {
      font-weight: bold;
      margin-top: 20px;
      font-size: 1.4em;
      text-align: center;
      color: #006699;
    }
    button {
      padding: 12px;
      background: #0077cc;
      color: white;
      border: none;
      border-radius: 8px;
      width: 100%;
      font-size: 1.1em;
      cursor: pointer;
      margin-top: 10px;
    }
    .price-type {
      margin-bottom: 15px;
      text-align: center;
    }
    .item-cost-display {
      font-size: 0.85em;
      color: #555;
      margin-left: 10px; /* Adjust spacing as needed */
      flex-shrink: 0; /* Prevent it from shrinking */
      text-align: right;
    }
    .equipment label input[type="checkbox"] {
      width: auto; /* Reset width for checkboxes */
      margin-left: 10px;
    }
     .equipment label input[type="number"] {
      width: 60px; /* Adjust width for number inputs */
      margin-left: auto; /* Push to the right */
    }
    .equipment label > span:first-child {
      flex-grow: 1; /* Allow the name to take available space */
    }
    /* Style for readonly guide fee input */
    #guideFeeInput {
        background-color: #f0f0f0; /* Light gray background */
        cursor: not-allowed; /* Indicate it's not editable */
    }
    .guide-fee-options {
        display: none; /* Hidden by default */
        text-align: center;
        margin-top: -10px; /* Pull it closer to the guide fee label */
        margin-bottom: 10px;
        padding: 5px 0;
        background-color: #f8f8f8;
        border-radius: 5px;
    }
    .guide-fee-options label {
        display: inline-block; /* Display radio buttons side by side */
        width: auto;
        margin: 0 10px;
    }
  </style>
</head>
<body>
  <h1>天空藍潛水費用(ง•̀_•́)ง</h1>

  <label>潛水天數：<input type="number" id="days" value="1" min="1"></label>

  <div class="price-type">
    <label><input type="radio" name="price" value="normal" checked> 一般價</label>
    <label><input type="radio" name="price" value="member"> 會員優惠價</label>
  </div>

  <label><input type="checkbox" id="hasLicense"> 已具潛水長(含)潛水證照（不計算導潛費）</label>

  <div class="equipment">
    <strong>選擇裝備：</strong>
    <label><span>面鏡/呼吸管</span><input type="checkbox" id="maskSnorkel" data-normal="50" data-member="50"><span class="item-cost-display"></span></label>
    <label><span>濕式防寒衣</span><input type="checkbox" id="wetsuit" data-normal="150" data-member="150"><span class="item-cost-display"></span></label>
    <label><span>套鞋</span><input type="checkbox" id="booties" data-normal="50" data-member="50"><span class="item-cost-display"></span></label>
    <label><span>蛙鞋</span><input type="checkbox" id="fins" data-normal="100" data-member="100"><span class="item-cost-display"></span></label>
    <label><span>BCD浮力補償裝置</span><input type="checkbox" id="bcd" data-normal="300" data-member="250"><span class="item-cost-display"></span></label>
    <label><span>調節器組</span><input type="checkbox" id="regulator" data-normal="300" data-member="250"><span class="item-cost-display"></span></label>
    <label><span>潛水電腦錶</span><input type="checkbox" id="computer" data-normal="300" data-member="300"><span class="item-cost-display"></span></label>
    <label><span>空氣氣瓶/支</span><input type="number" id="airTank" value="0" min="0" data-normal="200" data-member="150"><span class="item-cost-display"></span></label>
    <label><span>高氧氣瓶/支</span><input type="number" id="nitroxTank" value="0" min="0" data-normal="300" data-member="250"><span class="item-cost-display"></span></label>
    <label><span>住宿(通鋪)</span><input type="number" id="stay" value="0" min="0" data-normal="400" data-member="300"><span class="item-cost-display"></span></label>
    <label><span>共乘交通(台北-貢寮)</span><input type="checkbox" id="sharedTransport" data-normal="400" data-member="300"><span class="item-cost-display"></span></label>
    
    <label><span>導潛費（總支數）</span><input type="number" id="guideFeeInput" value="0" readonly><span class="item-cost-display" id="guideFeeDisplay"></span></label>
    <div class="guide-fee-options" id="memberGuideOptions">
        <label><input type="radio" name="memberGuideLevel" value="OW" checked> OW (每支 200 元)</label>
        <label><input type="radio" name="memberGuideLevel" value="AOW"> AOW (每支 100 元)</label>
    </div>
  </div>

  <button onclick="calculate()">計算總費用</button>

  <div class="result" id="result">總費用：$0 元</div>

  <script>
    function updateItemCost(inputElement) {
        const days = parseInt(document.getElementById("days").value);
        const priceType = document.querySelector("input[name='price']:checked").value;
        const displaySpan = inputElement.nextElementSibling; // Get the span element immediately following the input

        let unitPrice = 0;
        let subtotal = 0;

        // Skip guideFeeInput as it's calculated separately and its display handled specifically
        if (inputElement.id === 'guideFeeInput') {
            return 0;
        }

        const normalPrice = inputElement.getAttribute('data-normal');
        const memberPrice = inputElement.getAttribute('data-member');

        if (normalPrice === null || memberPrice === null) {
            if (displaySpan) displaySpan.textContent = ''; // Clear if no price data
            return 0;
        }

        unitPrice = parseInt(inputElement.getAttribute(`data-${priceType}`));

        if (inputElement.type === 'checkbox') {
            if (inputElement.checked) {
                if (inputElement.id === 'sharedTransport') {
                    subtotal = unitPrice; // One-time fee
                } else {
                    subtotal = unitPrice * days; // Daily rental for equipment
                }
            }
        } else if (inputElement.type === 'number') {
            const quantity = parseInt(inputElement.value);
            subtotal = quantity * unitPrice * days; // Tanks and accommodation are daily items
        }

        if (displaySpan) {
            if (subtotal > 0) {
                 displaySpan.textContent = `單價: $${unitPrice} / 總計: $${subtotal}`;
            } else {
                 displaySpan.textContent = ''; // Clear display if not selected or quantity is zero
            }
        }
        return subtotal;
    }

    function calculate() {
      const days = parseInt(document.getElementById("days").value);
      const priceType = document.querySelector("input[name='price']:checked").value;
      const hasLicense = document.getElementById("hasLicense").checked;
      let total = 0;

      // Update visibility of member guide fee options
      const memberGuideOptionsDiv = document.getElementById('memberGuideOptions');
      const guideFeeInput = document.getElementById("guideFeeInput");
      const guideFeeDisplaySpan = document.getElementById("guideFeeDisplay");

      if (priceType === 'member') {
          memberGuideOptionsDiv.style.display = 'block';
      } else {
          memberGuideOptionsDiv.style.display = 'none';
      }

      // Iterate through all relevant inputs to update their individual costs and sum them
      document.querySelectorAll(".equipment input[type='checkbox'], .equipment input[type='number']").forEach(input => {
        // Exclude the guideFeeInput from this general loop as it's calculated specifically later
        if (input.id !== 'guideFeeInput') {
            total += updateItemCost(input);
        }
      });

      // --- Guide fee calculation ---
      const airTankCount = parseInt(document.getElementById("airTank").value);
      const nitroxTankCount = parseInt(document.getElementById("nitroxTank").value);
      const totalTanks = airTankCount + nitroxTankCount; // Sum of air and nitrox tanks

      let guideFee = 0;
      let guideFeePerTank = 0;
      
      // Update the readonly input for total tanks *or* "我敲棒 不需付費"
      if (hasLicense) {
          guideFeeInput.value = ''; // Clear number value
          guideFeeDisplaySpan.textContent = '我敲棒 不需付費';
      } else {
          guideFeeInput.value = totalTanks;

          if (priceType === 'normal') {
            guideFeePerTank = 300; // Normal price is always 300 per tank
          } else { // priceType === 'member'
            const selectedMemberGuideLevel = document.querySelector("input[name='memberGuideLevel']:checked");
            if (selectedMemberGuideLevel) {
              if (selectedMemberGuideLevel.value === 'OW') {
                  guideFeePerTank = 200;
              } else if (selectedMemberGuideLevel.value === 'AOW') {
                  guideFeePerTank = 100;
              }
            } else {
              // Default to OW (200) if no member guide level is selected (e.g., on first load or price type switch)
              guideFeePerTank = 200;
              document.querySelector("input[name='memberGuideLevel'][value='OW']").checked = true; // Set OW as default
            }
          }
          guideFee = totalTanks * guideFeePerTank;

          // Update guide fee display span
          if (guideFee > 0) {
              guideFeeDisplaySpan.textContent = `單價: $${guideFeePerTank} / 總計: $${Math.round(guideFee)}`;
          } else {
              guideFeeDisplaySpan.textContent = ''; // Clear if guide fee is 0
          }
      }
      
      total += guideFee; // Add guide fee to the total (will be 0 if hasLicense is true)

      document.getElementById("result").textContent = `總費用：$${Math.round(total)} 元`;
    }

    // Add event listeners to all relevant inputs for real-time updates
    document.addEventListener('DOMContentLoaded', () => {
        document.getElementById("days").addEventListener('input', calculate);
        document.getElementById("hasLicense").addEventListener('change', calculate);

        // Listen for changes on price type radios
        document.querySelectorAll("input[name='price']").forEach(radio => {
            radio.addEventListener('change', calculate);
        });

        // Listen for changes on member guide level radios
        document.querySelectorAll("input[name='memberGuideLevel']").forEach(radio => {
            radio.addEventListener('change', calculate);
        });

        document.querySelectorAll(".equipment input[type='checkbox'], .equipment input[type='number']").forEach(input => {
            // Exclude the guideFeeInput from general listeners as it's updated programmatically
            if (input.id !== 'guideFeeInput') {
                input.addEventListener('change', calculate);
                if (input.type === 'number') {
                    input.addEventListener('input', calculate);
                }
            }
        });

        calculate(); // Initial calculation when the page loads
    });
  </script>
</body>
</html>
