
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <meta name="description" content="Arras.io is a fan-made sequel of diep.io! Survive and shoot at others while keeping your own tank alive!">
  <meta name="robots" content="index, follow">
  <meta property="og:image" content="/favicons/favicon-96x96.png">
  <meta property="og:image:width" content="52">
  <meta property="og:image:height" content="52">
  <title>Custom Shape Builder - Eclipse.io</title>
  <style>
  #output {
    font: 14px monospace;
    margin: 16px 4px;
    outline: none;
    border: 1px solid #ccc;
    border-radius: 5px;
    padding: 6px;
  }
  #add, #remove {
    margin: 0 4px 16px;
  }
  #calculator {
    width: 600px;
    height: 600px;
  }
  </style>
</head>
<body>
  <script src="https://www.desmos.com/api/v1.2/calculator.js?apiKey=dcb31709b452b1cf9dc26972add0fda6"></script>
  Your Code:
  <div id="output" contenteditable></div>
  <button id="add">Add Point</button>
  <button id="remove">Remove Point</button>
  <div id="calculator"></div>
  <script>
  let calculatorElement = document.getElementById('calculator')
  let addElement = document.getElementById('add')
  let removeElement = document.getElementById('remove')
  let outputElement = document.getElementById('output')

  let calculator = Desmos.GraphingCalculator(calculatorElement)
  calculator.updateSettings({
    expressionsCollapsed: true
  })
  calculator.setExpression({
    id: 'table',
    type: 'table',
    columns: [{
      latex: 'x_1',
      values: [-1, 0.5, 1, 1, 0.5, -1],
    }, {
      latex: 'y_1',
      values: [-1, -1, -0.5, 0.5, 1, 1],
      dragMode: Desmos.DragModes.XY,
      color: Desmos.Colors.BLACK,
    }],
  })
  calculator.setExpression({
    id: 'tableReconnect',
    latex: '\\operatorname{polygon}\\left(x_1,\\ y_1\\right)',
    color: Desmos.Colors.BLACK,
    fill: null,
  })
  calculator.setExpression({
    id: 'hitbox',
    latex: 'x^2+y^2=1',
    color: Desmos.Colors.RED,
  })
  calculator.setMathBounds({
    left: -2,
    right: 2,
    bottom: -2,
    top: 2,
  })


  let xList = calculator.HelperExpression({ latex: 'x_1' })
  let xListValue = [-1, 0.5, 1, 1, 0.5, -1]
  let yList = calculator.HelperExpression({ latex: 'y_1' })
  let yListValue = [-1, -1, -0.5, 0.5, 1, 1]

  xList.observe('listValue', () => {
    xListValue = xList.listValue
    updateCode()
  })
  yList.observe('listValue', () => {
    yListValue = yList.listValue
    updateCode()
  })

  let lock = false
  let updateCode = () => {
    if (lock) {
      lock = false
      return
    }
    let x = xListValue.slice()
    let y = yListValue.slice()
    let out = []
    while (x.length > 0 && y.length > 0) {
      out.push([x.shift(), y.shift()])
    }
    outputElement.style.color = '#000'
    outputElement.innerText = 'SHAPE: ' + JSON.stringify(out)
  }
  let updateExpression = () => {
    calculator.setExpression({
      id: 'table',
      type: 'table',
      columns: [{
        latex: 'x_1',
        values: xListValue,
      }, {
        latex: 'y_1',
        values: yListValue,
        lines: true,
        dragMode: Desmos.DragModes.XY,
        color: Desmos.Colors.BLACK,
      }],
    })
  }

  addElement.onclick = () => {
    xListValue.push(Math.round(Math.random() * 20 - 10) / 5)
    yListValue.push(Math.round(Math.random() * 20 - 10) / 5)
    updateExpression()
    updateCode()
  }
  removeElement.onclick = () => {
    if (xListValue.length > 1) {
      xListValue.pop()
      yListValue.pop()
    }
    updateExpression()
    updateCode()
  }
  outputElement.addEventListener('input', () => {
    let output = outputElement.innerText.match(/\[\s*\[.*]\s*]/) || ['']
    try {
      let points = JSON.parse(output)
      xListValue = points.map(r => r[0])
      yListValue = points.map(r => r[1])
      outputElement.style.color = '#000'
      lock = true
      updateExpression()
    } catch(e) {
      outputElement.style.color = '#b00'
    }
  }, false)
  updateCode()
  </script>
</body>
</html>
