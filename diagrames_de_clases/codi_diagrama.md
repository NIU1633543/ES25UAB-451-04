@startuml

package Entitats {
  '---------------------
  ' Enums
  '---------------------
  enum EstatComanda {
    PENDENT
    EN_PROCES
    EN_RUTA
    ENTREGADA
    CANCELADA
  }
  enum TipusAllergen {
    GLUTEN
    LACTOSA
    NOUS
    FRUITS_SECS
    ALTRES
  }
  enum TipusRecollida {
    REPARTIDOR_APP
    CUINER_ENTREGA
    CLIENT_RECULL
  }
  enum TipusSubscripcio {
    BASICA
    PREMIUM
  }
  enum TipusPagament {
    TARGETA
    PAYPAL
  }
  enum TipusUsuari {
    CLIENT
    CUINER
    REPARTIDOR
  }

  '---------------------
  ' Usuari
  '---------------------
  abstract class Usuari {
    - id: String
    - nom: String
    - email: String
    - password: String
    + validarCredencials(pwd: String): Boolean
  }

  class Subscripcio {
    - tipus: TipusSubscripcio
    - dataInici: DateTime
    - dataFi: DateTime
    + estaActiva(): Boolean
  }

  class Xat {
    - id: String
    + enviar(missatge: String): void
  }

  class Missatge {
    - autor: Usuari
    - text: String
    - time: DateTime
  }

  '---------------------
  ' Rols
  '---------------------
  class Client {
    - alergens: List<TipusAllergen>
    + crearComanda(linies: List<LiniaComanda>, tipusRecollida: TipusRecollida): Comanda
    + veureHistorial(): List<Comanda>
    + valorar(c: Comanda, v: Valoracio): void
  }

  class Cuiner {
    - disponiblitat: Boolean
    + acceptarProposta(): void
  }

  class Repartidor {
    - ubicacio: GeoLocation
    + assignar(c: Comanda): void
  }

  '---------------------
  ' Porductes
  '---------------------
  class Plat {
    - nom: String
    - ingredients: List<Ingredient> 
    - preu: Float
  }

  class Menu {
    - nom: String
    - preu: Float
  }

  class Ingredient {
    - nom: String
    - allergen?: TipusAllergen
  }

  

  '---------------------
  ' COMANDA y LINEA
  '---------------------
  class Comanda {
    - id: String
    - data: DateTime
    - estat: EstatComanda
    - tipusRecollida: TipusRecollida
    + calcularTotal(): Float
    + pagar(tipus: TipusPagament): Boolean
  }

  class LiniaComanda {
    - quantitat: Int
    - concepte: String
    - preu: Float
    + subtotal(): Float
    + propostaplat(p : plat) :void
  }

  class Pagament {
    - id: String
    - data: DateTime
    - mètode: TipusPagament
    - exit: Boolean
    + processar(): Boolean
  }

  class Valoracio {
    - puntuacio: Int
    - comentari: String
  }

  '---------------------
  ' loc
  '---------------------
  class GeoLocation {
    - lat: Double
    - lon: Double
  }

  '---------------------
  ' relacion
  '---------------------
  Usuari <|-- Client
  Usuari <|-- Cuiner
  Usuari <|-- Repartidor

  Client "1" -- "0..1" Subscripcio
  Client "1" -- "*" Comanda
  Client "1" -- "*" Xat
  Client "1" -- "*" Valoracio

  Xat "1" -- "*" Missatge
  Missatge --> Xat
  Cuiner "1" -- "*" Xat

  Comanda "1" -- "*" LiniaComanda
  Comanda "1" -- "1" Pagament
  Pagament --> Comanda
  Comanda "1" -- "0..5" Valoracio
  Valoracio --> Comanda

  LiniaComanda --> Plat
  LiniaComanda --> Menu
  
  Repartidor "1" -- "*" Comanda
}
@enduml
