# Tax-Calculator
New Tax Regime Calculator
<!DOCTYPE html>
<html>
<head>
    <title>Income Tax Calculator</title>
    <style>
        .container { max-width: 600px; margin: 20px auto; padding: 20px; }
        .form-group { margin-bottom: 15px; }
        label { display: block; margin-bottom: 5px; }
        input { width: 100%; padding: 8px; }
        button { background: #4CAF50; color: white; padding: 10px 20px; border: none; cursor: pointer; }
        #result { margin-top: 20px; padding: 15px; background: #f8f8f8; }
        table { width: 100%; border-collapse: collapse; margin-top: 10px; }
        th, td { border: 1px solid #ddd; padding: 8px; text-align: center; }
        th { background: #f4f4f4; }
    </style>
</head>
<body>
    <div class="container">
        <h2>Income Tax Calculator (New Regime)</h2>
        <div class="form-group">
            <label>Annual Income (₹):</label>
            <input type="number" id="income" required>
        </div>
        <button onclick="calculateTax()">Calculate Tax</button>
        <div id="result"></div>
    </div>

    <script>
        function calculateTax() {
            const income = parseFloat(document.getElementById('income').value);
            if (isNaN(income) || income < 0) {
                alert("Please enter a valid income amount.");
                return;
            }

            const standardDeduction = 75000;

            // Taxable income calculation
            let taxableIncome = income - standardDeduction;
            if (taxableIncome < 0) taxableIncome = 0;

            // Tax slabs
            const slabs = [
                { upper: 400000, rate: 0 },
                { upper: 800000, rate: 5 },
                { upper: 1200000, rate: 10 },
                { upper: 1600000, rate: 15 },
                { upper: 2000000, rate: 20 },
                { upper: 2400000, rate: 25 },
                { upper: Infinity, rate: 30 }
            ];

            let totalTax = 0;
            let previousUpper = 0;
            let remainingIncome = taxableIncome;
            let taxBreakdown = "";

            // Tax calculation per slab
            for (const slab of slabs) {
                if (remainingIncome <= 0) break;

                const slabRange = slab.upper - previousUpper;
                const incomeInSlab = Math.min(remainingIncome, slabRange);
                const taxForSlab = incomeInSlab * (slab.rate / 100);

                totalTax += taxForSlab;
                remainingIncome -= incomeInSlab;
                previousUpper = slab.upper;

                // Format the income range for display
                let incomeRange;
                if (slab.upper === Infinity) {
                    incomeRange = `> ₹24,00,000`;
                } else {
                    incomeRange = `₹${(previousUpper - slabRange + 1).toLocaleString()} - ₹${slab.upper.toLocaleString()}`;
                }

                // Add slab-wise breakdown
                taxBreakdown += `
                    <tr>
                        <td>${incomeRange}</td>
                        <td>${slab.rate}%</td>
                        <td>₹${incomeInSlab.toLocaleString()}</td>
                        <td>₹${taxForSlab.toFixed(2)}</td>
                    </tr>
                `;
            }

            // Round tax to 2 decimal places
            totalTax = Math.round(totalTax * 100) / 100;

            // Display results
            document.getElementById('result').innerHTML = `
                <h3>Tax Calculation Summary</h3>
                <p><strong>Annual Income:</strong> ₹${income.toLocaleString()}</p>
                <p><strong>Standard Deduction:</strong> ₹${standardDeduction.toLocaleString()}</p>
                <p><strong>Taxable Income:</strong> ₹${taxableIncome.toLocaleString()}</p>
                <h3>Slab-wise Tax Calculation</h3>
                <table>
                    <tr>
                        <th>Income Range</th>
                        <th>Tax Rate</th>
                        <th>Income in Slab</th>
                        <th>Tax Amount</th>
                    </tr>
                    ${taxBreakdown}
                </table>
                <h3>Total Income Tax: ₹${totalTax.toLocaleString()}</h3>
            `;
        }
    </script>
</body>
</html>
