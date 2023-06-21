<script>
	import Chart from "chart.js/auto";
	import {onMount} from "svelte";

	onMount(() => {
		const ctx = document.getElementById("chartOne");
		document.getElementById("jsonInput").addEventListener("submit", (e) => {
			e.preventDefault();
			const fileInput = document.getElementById("jsonFileInput");
			let file = fileInput.files[0];

			if (file) {
				var reader = new FileReader();

				reader.onload = function (e) {
					const contents = e.target.result;
					let jsonData = JSON.parse(contents);
					// Use the jsonData object as needed

					handleData(jsonData);
				};
			}

			reader.readAsText(file);
		});

		function handleData(data) {
			var filteredTransactions = data.reduce(function (acc, transaction) {
				var existingTransaction = acc.find(function (item) {
					return item.date === transaction.Date;
				});

				if (existingTransaction) {
					existingTransaction.y = transaction.Balance; // Update the type
				} else {
					acc.push({
						x: transaction.Date,
						y: parseFloat(transaction.Balance.replace(/[^0-9.-]+/g, "")),
					});
				}

				return acc;
			}, []);
			makeLineGraph(filteredTransactions);
			console.log(filteredTransactions);
		}

		function makeLineGraph(data) {
			const options = {
				borderWidth: 5,
				tension: 0.15,
				pointRadius: 0,
				pointHoverRadius: 5,
				pointHitRadius: 20,
				backgroundColor: "#000",
				borderColor: "#000",
				responsive: true,
				scales: {
					x: {
						title: {
							display: true,
							text: "test",
						},
						type: "category",
						border: {
							display: true,
							color: "#000",
							width: 3,
							z: -5,
						},
						grid: {
							display: false,
							drawOnChartArea: false,
						},
						suggestedMin: 5,
						suggestedMax: 30,
					},
					y: {
						backgroundColor: "#00000000",
						ticks: {
							color: "#000",
							textStrokeWidth: 200,
						},
						type: "linear",
						border: {
							display: true,
							color: "#000",
							width: 3,
							z: -5,
						},
						grid: {
							display: false,
							drawOnChartArea: false,
						},
						title: {
							display: true,
							text: "test",
						},
					},
				},
			};

			new Chart(ctx, {
				type: "line",
				data: {
					datasets: [
						{
							label: "# of Votes",
							data: data,
							borderWidth: 2.5,
							tension: 0.15,
							pointRadius: 0,
							pointHoverRadius: 5,
							pointHitRadius: 20,
							backgroundColor: "#000",
							borderColor: "#000",
						},
					],
				},
				options,
			});
		}
	});
</script>

<div>
	<!-- svelte-ignore a11y-no-interactive-element-to-noninteractive-role -->
	<canvas id="chartOne" role="img">
		We ran into a problem loading thos chart! Make sure you have javascript
		enabled for this website!
	</canvas>
	<form id="jsonInput">
		<input type="file" id="jsonFileInput" accept=".json" />
		<button type="submit">Submit</button>
	</form>
</div>

<style>
	div {
		color: rgb(116, 116, 116);
	}
</style>
