# awsStepFunctionsBedrock

Projeto: Assistente de Delivery com AWS Step Functions + Bedrock
📌 Objetivo

Criar um fluxo automatizado de pedidos de delivery, onde o usuário pode interagir com um assistente de IA (via Bedrock) e o processo do pedido é coordenado por AWS Step Functions.

🔹 Fluxo de Funcionamento

Usuário faz o pedido

O cliente envia uma mensagem (ex.: “Quero 2 pizzas grandes e uma coca”).

O Bedrock interpreta a intenção e gera uma resposta estruturada (ex.: produto, quantidade, endereço, forma de pagamento).

Orquestração com Step Functions

Estados do fluxo:

Capturar pedido (IA – Bedrock)

Validar itens (consultar catálogo)

Calcular preço total

Confirmar com o usuário (via Bedrock ou API de chatbot)

Processar pagamento (integração com API de pagamento)

Acionar entrega (API da logística ou motoboy)

Finalizar pedido

Resposta ao usuário

O Bedrock gera mensagens personalizadas:

“Seu pedido foi confirmado!”

“Tempo estimado de entrega: 35 minutos.”
Exemplo Simplificado com Step Functions
{
  "Comment": "Assistente de Delivery com Step Functions e Bedrock",
  "StartAt": "CapturarPedido",
  "States": {
    "CapturarPedido": {
      "Type": "Task",
      "Resource": "arn:aws:states:::bedrock:invokeModel",
      "Parameters": {
        "Input": "$.mensagemUsuario",
        "ModelId": "anthropic.claude-v2"
      },
      "Next": "ValidarItens"
    },
    "ValidarItens": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:validar-itens",
      "Next": "CalcularPreco"
    },
    "CalcularPreco": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:calcular-preco",
      "Next": "ConfirmarPedido"
    },
    "ConfirmarPedido": {
      "Type": "Task",
      "Resource": "arn:aws:states:::bedrock:invokeModel",
      "Parameters": {
        "Input": "$.detalhesPedido",
        "ModelId": "anthropic.claude-v2"
      },
      "Next": "ProcessarPagamento"
    },
    "ProcessarPagamento": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:processar-pagamento",
      "Next": "AcionarEntrega"
    },
    "AcionarEntrega": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:acionar-entrega",
      "End": true
    }
  }
}

Funções Lambda (exemplo em Python)
def lambda_handler(event, context):
    itens_validos = []
    for item in event.get("itens", []):
        if item in ["pizza", "coca", "hamburguer"]:  # catálogo fictício
            itens_validos.append(item)
    return {"itens_validados": itens_validos}
📌 Cálculo de preço
    def lambda_handler(event, context):
    precos = {"pizza": 40, "coca": 8, "hamburguer": 25}
    total = sum(precos.get(item, 0) for item in event.get("itens_validados", []))
    return {"total": total}
