# Estrategia-Double-Blaze
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Estratégia Padrão Cruzado de Tendência</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(to right, #e9ecef, #f8f9fa);
      padding: 20px;
      color: #343a40;
    }
    table {
      width: 100%;
      border-collapse: separate;
      border-spacing: 0;
      border-radius: 12px;
      overflow: hidden;
      background: #fdfdfd;
      box-shadow: 0 0 8px rgba(0,0,0,0.05);
      margin-bottom: 20px;
    }
    th, td {
      border: 1px solid #ced4da;
      padding: 10px;
      text-align: center;
    }
    th {
      background: #dee2e6;
      color: #495057;
    }
    input {
      width: 90%;
      padding: 5px;
      text-align: center;
      border-radius: 6px;
      border: 1px solid #ced4da;
      background: #f1f3f5;
      color: #343a40;
    }
    button {
      padding: 10px 20px;
      background: #6c757d;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      font-size: 16px;
      margin-right: 10px;
    }
    button:hover {
      background: #5a6268;
    }
    .sugestao, .estrategia2, .numerologia {
      font-weight: bold;
    }
    .verde {
      background-color: #d4edda !important;
    }
    .preenchido {
      background-color: #e9f7ef;
    }
  </style>
</head>
<body>
  <h1>Estratégia: Padrão Cruzado de Tendência</h1>

  <table>
    <thead>
      <tr>
        <th>Rodada</th>
        <th>Horário</th>
        <th>Nº da Pedra</th>
        <th>Resultado (V/P/B)</th>
        <th>Soma</th>
        <th>Sugestão</th>
        <th>Estratégia 2</th>
        <th>Numerologia</th>
      </tr>
    </thead>
    <tbody id="tabela"></tbody>
  </table>

  <button onclick="gerarMaisRodadas()">Carregar Mais Rodadas</button>
  <button onclick="gerarGrafico()">Gerar Gráfico</button>
  <button onclick="resetarPlanilha()">Resetar Planilha</button>

  <canvas id="graficoResultados" width="400" height="150"></canvas>

  <script>
    let totalRodadas = 0;

    function gerarMaisRodadas(qtd = 10) {
      const tbody = document.getElementById("tabela");
      for (let i = totalRodadas + 1; i <= totalRodadas + qtd; i++) {
        const linha = document.createElement("tr");
        linha.innerHTML = `
          <td>${i}</td>
          <td><input type="text" id="hora-${i}" maxlength="5" placeholder="hh:mm" oninput="formatarHora(this);calcular(${i})"></td>
          <td><input type="number" id="pedra-${i}" min="0" max="36" oninput="calcular(${i})"></td>
          <td><input type="text" maxlength="1" id="resultado-${i}" oninput="calcular(${i})"></td>
          <td id="soma-${i}">---</td>
          <td id="sugestao-${i}" class="sugestao">---</td>
          <td id="estrategia2-${i}" class="estrategia2">---</td>
          <td id="numerologia-${i}" class="numerologia">---</td>
        `;
        tbody.appendChild(linha);
      }
      totalRodadas += qtd;
    }

    function formatarHora(input) {
      let v = input.value.replace(/[^0-9]/g, "");
      if (v.length >= 3) input.value = v.slice(0, 2) + ':' + v.slice(2, 4);
    }

    function calcular(id) {
      const horaInput = document.getElementById(`hora-${id}`).value;
      const pedra = parseInt(document.getElementById(`pedra-${id}`).value);
      const resultado = document.getElementById(`resultado-${id}`).value.toUpperCase();
      const linha = document.getElementById(`hora-${id}`).closest('tr');

      if (horaInput) linha.cells[1].classList.add("preenchido");
      if (!isNaN(pedra)) linha.cells[2].classList.add("preenchido");
      if (["V", "P", "B"].includes(resultado)) linha.cells[3].classList.add("preenchido");

      if (!horaInput || isNaN(pedra)) return;

      const [h, m] = horaInput.split(":").map(Number);
      const soma = m + pedra;
      document.getElementById(`soma-${id}`).textContent = soma;

      const resultados = [];
      for (let i = 1; i <= totalRodadas; i++) {
        const val = document.getElementById(`resultado-${i}`)?.value.toUpperCase();
        if (["V", "P", "B"].includes(val)) resultados.push(val);
      }

      let ultimos = resultados.slice(-5).join("");
      let freqV = resultados.filter(r => r === "V").length;
      let freqP = resultados.filter(r => r === "P").length;
      let distB = resultados.lastIndexOf("B") === -1 ? 99 : resultados.length - 1 - resultados.lastIndexOf("B");

      let sugestao = "Análise insuficiente";
      if (soma % 5 === 0 && distB >= 10) sugestao = "Alta chance de Branco";
      else if (/^(VP){2,}|(PV){2,}/.test(ultimos)) sugestao = freqV > freqP ? "Apostar em Vermelho" : "Apostar em Preto";
      else if (ultimos.endsWith("VVV") || ultimos.endsWith("PPP")) sugestao = "Apostar em inversão";

      document.getElementById(`sugestao-${id}`).textContent = sugestao;

      let estrategia2 = "---";
      if (ultimos.endsWith("VPVP")) estrategia2 = "Inversão para Vermelho";
      else if (ultimos.endsWith("PVPV")) estrategia2 = "Inversão para Preto";
      document.getElementById(`estrategia2-${id}`).textContent = estrategia2;

      let numerologia = "---";
      switch (pedra) {
        case 11: numerologia = "Indica Branco ou Preto"; break;
        case 13: numerologia = "Comportamento Neutro"; break;
        case 12: numerologia = "Finalizador de Preto"; break;
        case 14: numerologia = "Traiçoeiro"; break;
        case 1: numerologia = "Vermelho ou Divisor"; break;
        case 8: numerologia = "Início de Preto"; break;
        case 3: numerologia = "Finalizador de Vermelho"; break;
        case 2: numerologia = "Puxa Vermelho"; break;
        case 4: numerologia = "Surf de Vermelho (com 6)"; break;
        case 5: numerologia = "Puxa Vermelho ou Divisor"; break;
        case 6: numerologia = "Padrão Invertido (com 4)"; break;
        case 7: numerologia = "Traiçoeiro"; break;
        default: numerologia = "---";
      }
      document.getElementById(`numerologia-${id}`).textContent = numerologia;

      const all = [sugestao, estrategia2, numerologia];
      if (all.every(s => s.includes("Vermelho") || s.includes("Branco"))) {
        linha.classList.add("verde");
      } else {
        linha.classList.remove("verde");
      }
    }

    function gerarGrafico() {
      const resultados = [];
      for (let i = 1; i <= totalRodadas; i++) {
        const val = document.getElementById(`resultado-${i}`)?.value.toUpperCase();
        if (["V", "P", "B"].includes(val)) resultados.push(val);
      }
      const vermelho = resultados.filter(r => r === "V").length;
      const preto = resultados.filter(r => r === "P").length;
      const branco = resultados.filter(r => r === "B").length;

      new Chart(document.getElementById('graficoResultados'), {
        type: 'bar',
        data: {
          labels: ['Vermelho', 'Preto', 'Branco'],
          datasets: [{
            label: 'Frequência de Resultados',
            data: [vermelho, preto, branco],
            backgroundColor: ['#f44336', '#495057', '#ffffff'],
            borderColor: ['#c62828', '#343a40', '#ced4da'],
            borderWidth: 1
          }]
        },
        options: {
          scales: {
            y: { beginAtZero: true }
          }
        }
      });
    }

    function resetarPlanilha() {
      document.getElementById("tabela").innerHTML = "";
      totalRodadas = 0;
      gerarMaisRodadas(20);
    }

    gerarMaisRodadas(20);
  </script>
</body>
</html>
