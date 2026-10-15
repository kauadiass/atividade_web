# Modelagem — Projeto 1

**Aluno:** Kauã
**Domínio:** Controle de pedidos e entregas de uma distribuidora de bebidas

## 1. O domínio

A distribuidora recebe pedidos de clientes (bares, mercados) e programa a entrega de cada pedido. Cada entrega tem data prevista e só é considerada atrasada se passar dela sem confirmação.

## 2. Entidades

| Entidade|          Atributos                 | Relacionamentos |
| Cliente |        id, nome, telefone          | faz vários pedidos |
| Pedido  | id, clienteId, dataPedido, status  | pertence a um cliente; gera uma entrega |
| Entrega | id, pedidoId, dataPrevista, status | pertence a um pedido |

## 3. Diagrama de classes

class Cliente {
  +id
  +nome
  +telefone
}

class Pedido {
  +id
  +clienteId
  +dataPedido
  +status
  +cancelar()
}

class Entrega {
  +id
  +pedidoId
  +dataPrevista
  +status
  +estaAtrasada(hoje)
  +marcarEntregue()
}

class PedidoService {
  +criar(clienteId)
  +cancelar(pedidoId)
}

class EntregaService {
  +programar(pedidoId, dataPrevista)
  +confirmar(entregaId)
  +listarAtrasadas()
}

class ClienteRepository {
  <<interface>>
  +buscarPorId(id)
  +salvar(cliente)
}

class PedidoRepository {
  <<interface>>
  +buscarPorId(id)
  +salvar(pedido)
}

class EntregaRepository {
  <<interface>>
  +buscarPorId(id)
  +listarPendentes()
  +salvar(entrega)
}

class Notificador {
  <<interface>>
  +enviar(destinatario, mensagem)
}

Cliente "1" --> "0..*" Pedido : faz
Pedido "1" --> "0..1" Entrega : gera
PedidoService ..> ClienteRepository : usa
PedidoService ..> PedidoRepository : usa
EntregaService ..> PedidoRepository : usa
EntregaService ..> EntregaRepository : usa
EntregaService ..> Notificador : usa


## 4. Responsabilidades

| Entidade| Regra (service) | Dados (repository) |
| Cliente | PedidoService   | ClienteRepository |
| Pedido  | PedidoService   | PedidoRepository |
| Entrega | EntregaService  | EntregaRepository |

|            Classe              | Único motivo para mudar |
| Cliente, Pedido, Entrega       | regras do próprio objeto (ex.: quando uma entrega está atrasada) |
| PedidoService / EntregaService | o processo de pedir e de entregar |
|          Repositories          | a forma de guardar (memória na S3, SQLite na S5) |
|          Notificador           | o canal de aviso (e-mail, SMS, WhatsApp) |

## 5. SRP e DIP

- **SRP:** nenhuma entidade salva a si mesma. Guardar é dos repositories, avisar é do Notificador, e cada processo fica no seu service.
- **DIP:** os services dependem dos contratos (repositories e Notificador), recebidos pelo construtor. 

## 6. Fora do escopo (YAGNI)

llogin, pagamento, estoque, rotas de caminhão e relatórios não entram no projeto.
