# pagarme-ruby
[![Build Status](https://travis-ci.org/pagarme/pagarme-ruby.png)](https://travis-ci.org/pagarme/pagarme-ruby)

Pagar.me Ruby library

## Documentation

* [Documentation](https://pagar.me/docs)
* [Full API Guide](https://docs.pagar.me/api)

## Getting Started

### Install

```shell
gem install pagarme
```
or add the following line to Gemfile:

```ruby
gem 'pagarme'
```
and run `bundle install` from your shell.

### Configure your API key

You can set your API key in Ruby:

```ruby
PagarMe.api_key = 'YOUR_API_KEY_HERE'
```

or set the environment variable _PAGARME\_API\_KEY_ (**recommended**)

### Using Pagar.me Checkout

See our [demo checkout](https://pagar.me/checkout).

More about how to use it [here](https://docs.pagar.me/checkout).

### Transactions

#### Creating a Credit Card Transaction

To create a credit card transaction, you need a [card\_hash](https://docs.pagar.me/capturing-card-data).

```ruby
  PagarMe::Transaction.new(
    amount:    1000,      # in cents
    card_hash: card_hash  # how to get a card hash: docs.pagar.me/capturing-card-data
  ).charge
```

More about [Creating a Credit Card Transaction](https://docs.pagar.me/transactions/#realizando-uma-transacao-de-cartao-de-credito).

#### Creating a Boleto Transaction

```ruby
  transaction = PagarMe::Transaction.new(
    amount:         1000,    # in cents
    payment_method: 'boleto'
  )
  transaction.charge

  transaction.boleto_url     # => boleto's URL
  transaction.boleto_barcode # => boleto's barcode
```

More about [Creating a Boleto Transaction](https://docs.pagar.me/transactions/#realizando-uma-transacao-de-boleto-bancario).

#### Split Rules

With split rules, received amount could be splitted between more than one recipient.
For example, splitting equally a transaction:

```ruby
  PagarMe::Transaction.new(
    amount:    1000,      # in cents
    card_hash: card_hash, # how to get a card hash: docs.pagar.me/capturing-card-data
    split\_rules: [
      { recipient_id: recipient_id_1, percentage: 50 },
      { recipient_id: recipient_id_2, percentage: 50 }
    ]
  ).charge
```

More about [Split Rules](https://docs.pagar.me/api/#regras-do-split).

### Plans & Subscriptions

You can use recurring charges, learn more [here](https://docs.pagar.me/plans-subscriptions).

It's important to understand the charges flow, learn more [here](https://docs.pagar.me/plans-subscriptions/#fluxo-de-cobranca)

#### Creating a Plan

```ruby
  PagarMe::Plan.new(
    amount: 4990,
    days:   30,
    name:   'Gold Plan'
  ).create
```

More about [Creating a Plan](https://docs.pagar.me/plans-subscriptions/#criando-um-plano).

#### Creating a Subscription

```ruby
  PagarMe::Subscription.new(
    plan:      PagarMe::Plan.find_by_id('1234'),
    card_hash: card_hash,
    customer:  { email: 'customer_email@pagar.me' }
  ).create
```

More about [Creating a Subscription](https://docs.pagar.me/plans-subscriptions/#criando-uma-assinatura).

### Recipients

#### Creating a Recipient

To create a recipient, so it can receive payments through split rules or transfers:

```ruby
  PagarMe::Recipient.create(
    bank_account: {
      bank_code:       '237',
      agencia:         '1935',
      agencia_dv:      '9',
      conta:           '23398',
      conta_dv:        '9',
      legal_name:      'Fulano da Silva',
      document_number: '00000000000000' # CPF or CNPJ
    },
    transfer_enabled: false
  )
```

More about [Creating a Recipient](https://docs.pagar.me/api/#recebedores).

#### Transfer Available Amout to Bank Account Manually

This is only needed if _transfer\_enabled_ is set to false. If set to true,
_transfer\_interval_ and _transfer\_day_ will handle it automatically.

```ruby
  PagarMe::Recipient.find(recipient_id).receive amount
```

### Balance And Balance Operations

#### Checking Balance

```ruby
  balance = PagarMe::Balance.balance
  balance.waiting_funds.amount # money to be received in your account
  balance.available.amount     # in your pagarme account
  balance.transferred.amount   # transferred to your bank account
```

Just that!

More about [Balance](https://docs.pagar.me/api/#saldo)

#### Checking Balance Operations

To access the history of balance operations:

```ruby
  PagarMe::BalanceOperation.balance_operations
```

Paginating:

```ruby
  PagarMe::BalanceOperation.balance_operations 2, 50 # second page, 50 per page
```

More about [Balance Operations](https://docs.pagar.me/api/#operacoes-de-saldo)

#### Checking Recipient Balance

```ruby
  balance = PagarMe::Recipient.find(recipient_id).balance
  balance.waiting_funds.amount # money to be received in his account
  balance.available.amount     # in his pagarme account
  balance.transferred.amount   # transferred to his bank account
```

Just that!

More about [Recipient Balance](https://docs.pagar.me/api/#saldo-de-um-recebedor)

#### Checking Recipient Balance Operations

To access the history of balance operations:

```ruby
  PagarMe::Recipient.find(recipient_id).balance_operations
```

Paginating:

```ruby
  PagarMe::Recipient.find(recipient_id).balance_operations 2, 50 # second page, 50 per page
```

More about [Recipient Balance Operations](https://docs.pagar.me/api/#operacoes-de-saldo-de-um-recebedor)

### Undocumented Features

This gem is stable, but in constant development.

This README is just a quick abstract of it's main features.

You can easily browse it's source code to see all [supported resources](https://github.com/pagarme/pagarme-ruby/tree/master/lib/pagarme/resources).

We will document everything while adding support to all resources listed in
[Full API Guide](https://docs.pagar.me/api).

Feel free to help us to add support to features sending pull requests.

Thanks!

### TODO

Add support to:

* [BulkAnticipation](https://docs.pagar.me/api/#antecipacoes)

Add support to [ElasticSearch Query DSL](https://docs.pagar.me/api/#buscas-avancadas),
so you can search your data optimally.

And document all the source code.

## License

Pagar.me Ruby library is released under the [MIT License](http://www.opensource.org/licenses/MIT).
