This is a [Next.js](https://nextjs.org/) project bootstrapped with [`create-next-app`](https://github.com/vercel/next.js/tree/canary/packages/create-next-app).

## Arqutectura por capas
### Capa de Presentación:
```
- pages/: En Next.js, las páginas se encuentran dentro de esta carpeta. Aquí se colocan las páginas específicas del sitio web, por ejemplo, index.js, votacion.js, resultado.js, etc.
- components/: Aquí se colocan los componentes reutilizables de React que se utilizan para construir las páginas.
```
### Capa de Aplicación:
```
- pages/api/: Se crea API routes dentro de esta carpeta. Aquí es donde se implementarías la lógica de la API para manejar las solicitudes de los clientes y comunicarse con la capa de dominio o servicios.
```
### Capa de Dominio:
```
- domain/: En esta carpeta, se colocan los modelos de dominio y la lógica de negocio. Por ejemplo, clases o funciones que representan entidades y reglas de negocio.
- domain/models: Se ingresan los especificos específicas del sitio web (Peson.js, Elector.js, etc.)
- domain/services: Se ingresa los servicios relacionados con la entidad
```
### Capa de Infraestructura:
```
- data/: Aquí coloca la implementación concreta de la lógica de infraestructura, como la conexión a la base de datos, llamadas a servicios externos, almacenamiento en caché, etc.
- services/: En esta carpeta, colocarías los servicios y utilidades que son específicos de la infraestructura, como servicios de autenticación, servicios de almacenamiento en la nube, etc.
- config/: Aquí colocarías la configuración de la aplicación, como variables de entorno, configuración de la base de datos, etc.
```
## Trabajo encargado:

![Casos de uso](https://github.com/LeonDavisCoropuna/Final-Ing-Software-I/edit/main/public/UseCaseDiagram1.png)

## Estilos de programación

### 1. Tantrum (pages/api/voteElector/politicalParty.js)
Estilo de programacion para el manejo de errores a traves de excepciones:

Ejemplo
```javascript
export default async function handlePartidoPolitico(req, res) {
  try {
    // Obtenemos los datos del partido político desde el servicio VoteElector
    const datosPartidoPolitico = await VoteElector.getPoliticalParty();

    // Devolvemos los datos en la respuesta con estado 200 (éxito)
    return res.status(200).json(datosPartidoPolitico);
  } catch (err) {
    // Manejamos los errores y proporcionamos un mensaje de error claro
    res.status(500).json({ error: "Error interno del servidor" });
  }
}
```
### 2. Letterbox (domain/services/ElectorService.js)

Este estilo de programación se basa en la idea de que cada entidad en el sistema (la "cápsula") es independiente y solo se comunica mediante mensajes. Es una forma de lograr un acoplamiento más bajo entre los componentes de un sistema y promueve la modularidad y la escalabilidad.

El código emplea la clase ElectorService como una cápsula de datos para guardar votos y obtener partidos políticos. Cada función en ElectorService tiene una única responsabilidad de recibir y despachar mensajes. La comunicación con el repositorio ElectorRepository es a través de mensajes usando llamadas await. Además, el código envía mensajes al repositorio para guardar votos o obtener partidos

Ejemplo: 
```javascript
import ElectorRepository from "../../data/repository/ElectorRepository"; // Ruta relativa para ElectorRepository
import Vote from "../../domain/models/Vote"; // Ruta relativa para Vote model

class ElectorService {
  // Función para guardar un voto
  static async saveVote(vote) {
    // Creamos una instancia del modelo Vote con los datos recibidos
    const instanceVote = new Vote(vote.idElector, vote.idPoliticalParty, vote.date);

    try {
      // Llamada al repositorio ElectorRepository para guardar el voto
      return await ElectorRepository.saveVote(instanceVote);
    } catch (error) {
      return error; // Devuelve el error en caso de fallo en la llamada al repositorio
    }
  }

  // Función para obtener los partidos políticos
  static async getPoliticalParty() {
    try {
      // Llamada al repositorio ElectorRepository para obtener los partidos políticos
      return await ElectorRepository.getPoliticalParty();
    } catch (error) {
      return error; // Devuelve el error en caso de fallo en la llamada al repositorio
    }
  }
}

export default ElectorService;
```
### 3. code-golf (pages/votacion.js)

Tan pocas líneas de código como sea posible.

Las funciones de flecha proporcionan una forma concisa de escribir funciones en JavaScript.

Ejemplo:
```javascript
// Obtiene el perfil del usuario desde el backend
  const getProfile = async () => {
    const res = await axios.get("/api/profile");
    setVote({
      ...vote,
      idElector: res.data.id,
    });
  };
```
## Convenciones de programación aplicados:
## Prácticas Codificación legible

### Comment Rules
Código con Comentarios (public/bananas_corp.sql)

```bash
-- --------------------------------------------------------

--
-- Estructura de tabla para la tabla `administrador`
--

DROP TABLE IF EXISTS `administrador`;
CREATE TABLE `administrador` (
  `id` int(11) NOT NULL,
  `cargo` varchar(100) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;

--
-- Volcado de datos para la tabla `administrador`
--

INSERT INTO `administrador` (`id`, `cargo`) VALUES
(3, 'Gerente de Sistemas ');

-- --------------------------------------------------------

--
-- Estructura de tabla para la tabla `candidato`
--

DROP TABLE IF EXISTS `candidato`;
CREATE TABLE `candidato` (
  `id` int(11) NOT NULL,
  `cargo` varchar(50) DEFAULT NULL,
  `id_partido` int(11) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;

--
-- Volcado de datos para la tabla `candidato`
--

INSERT INTO `candidato` (`id`, `cargo`, `id_partido`) VALUES
(1, 'Presidente', 1),
(4, 'Presidente', 2),
(5, 'vicePresidente', 1);

-- --------------------------------------------------------

--
-- Estructura de tabla para la tabla `electores`
--

DROP TABLE IF EXISTS `electores`;
CREATE TABLE `electores` (
  `id` int(11) NOT NULL,
  `email` varchar(100) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;
```
### Limit Line Length y Uso de Identación

Límite de caracteres por línea y uso de sangría en el código (domains/models/Person.j)

```javascript
class Person {
    constructor(id, name, lastName, username) {
        this.id = id;
        this.name = name;
        this.lastName = lastName;
        this.username = username;
    }
    // Setters
    set id(value) {
        this._id = value;
    }
    set name(value) {
        this._name = value;
    }
    set lastName(value) {
        this._lastName = value;
    }
    set username(value) {
        this._username = value;
    }

```
### Names Rules

se utilizo el tipo de nomenclatura camelCase (domains/models/Person.j)

```javascript
class ResultVote {
    constructor(id, politicalParty, president, numVotes) {
        this._id = id;
        this._politicalParty = politicalParty;
        this._president = president;
        this._numVotes = numVotes;
    }
```
- Separation of Code and Data: El código realiza operaciones en la base de datos y crea instancias de objetos PoliticalParty, lo que muestra una separación de lógica de negocio y datos.
    - Object-Oriented vs. Procedural: El código utiliza programación orientada a objetos al crear instancias de la clase PoliticalParty y manipular los resultados obtenidos de la base de datos.

```javascript
import { pool } from "@/ldavis/data/config/db";
import Candidate from "@/ldavis/domain/models/Candidate";
import PoliticalParty from "@/ldavis/domain/models/PoliticalParty";
import { responseEncoding } from "axios";

class ElectorRepository {
    static async saveVote(vote) {
        try {
            const [find] = await pool.query("SELECT * FROM votos WHERE id_elector = ?", vote._idElector);
            if (find.length > 0) {
                return {status: 401};
            }
            const resp = await pool.query("INSERT INTO votos VALUES (?,?,?);", [vote._idElector, vote._idPoliticalParty, vote._date]);
            return {status: 200};
        } catch (err) {
            return {status: 500};
            //throw new Error("Error en la base de datos"); // o cualquier otro mensaje de error
        }
    }

    static async getPoliticalParty() {
        try {
            const [result] = await pool.query("SELECT * FROM partido_politico;");
            const listPoliticalParty = [];
            result.map(e => {
                const newPoliticalParty = new PoliticalParty(e.id, e.partido);
                listPoliticalParty.push(newPoliticalParty);
            });
            return listPoliticalParty;
        } catch (err) {
            return err;
        }
    }
}

export default ElectorRepository;
```
## Principios SOLID
### 1. Single Responsibility Principle (SRP)

La clase VoteElector contiene dos funciones bien definidas y especializadas. La primera función, sendVote(vote), se encarga de enviar un voto utilizando el servicio ElectorService para su almacenamiento y maneja los posibles errores relacionados con esta operación. La segunda función, getPoliticalParty(), se encarga de obtener los partidos políticos utilizando el mismo servicio y maneja la lógica de consulta y los errores que puedan surgir. Ambas funciones cumplen con el principio de Single Responsibility (SRP) al tener una única responsabilidad y realizar una tarea específica.

```javascript
class VoteElector {
  // Función para enviar un voto
  static async sendVote(vote) {
    try {
      // Llamada al servicio ElectorService para guardar el voto
      return await ElectorService.saveVote(vote);
    } catch (error) {
      return error; // Devuelve el error en caso de fallo en la llamada al servicio
    }
  }

  // Función para obtener los partidos políticos
  static async getPoliticalParty() {
    try {
      // Llamada al servicio ElectorService para obtener los partidos políticos
      return await ElectorService.getPoliticalParty();
    } catch (error) {
      return error; // Devuelve el error en caso de fallo en la llamada al servicio
    }
  }
}

```
### 2. Open/Closed Principle (OCP)
“Deberías ser capaz de extender el comportamiento de una clase, sin modificarla”. En otras palabras: las clases que usas deberían estar abiertas para poder extenderse y cerradas para modificarse.

El constructor de la clase ElectorService se utiliza para recibir un parámetro llamado repository, que representa el repositorio de datos utilizado para interactuar con el almacenamiento. Al asignar este parámetro a la propiedad this.repository, la clase puede acceder al repositorio y realizar operaciones dentro de sus métodos, lo que la hace flexible para trabajar con diferentes tipos de repositorios sin modificar su código interno. 

```javascript
import ElectorRepository from "../../data/repository/ElectorRepository"; // Ruta relativa para ElectorRepository
import Vote from "../../domain/models/Vote"; // Ruta relativa para Vote model

class ElectorService {
  // Constructor de la clase ElectorService.
  // El constructor se ejecuta al crear una instancia de ElectorService.
  // Recibe un parámetro "repository" que representa el repositorio de datos a utilizar.
  constructor(repository) {
    // Asigna el repositorio recibido como parámetro a la propiedad "repository".
    this.repository = repository;
  }

  // Función para guardar un voto.
  // Recibe el voto a guardar como parámetro.
  async saveVote(vote) {
    // Creamos una instancia del modelo Vote con los datos recibidos.
    const instanceVote = new Vote(vote.idElector, vote.idPoliticalParty, vote.date);

    try {
      // Llamada al repositorio ElectorRepository para guardar el voto.
      // Utiliza el repositorio asignado en el constructor para realizar la operación.
      return await this.repository.saveVote(instanceVote);
    } catch (error) {
      return error; // Devuelve el error en caso de fallo en la llamada al repositorio.
    }
  }

  // Función para obtener los partidos políticos.
  async getPoliticalParty() {
    try {
      // Llamada al repositorio ElectorRepository para obtener los partidos políticos.
      // Utiliza el repositorio asignado en el constructor para realizar la operación.
      return await this.repository.getPoliticalParty();
    } catch (error) {
      return error; // Devuelve el error en caso de fallo en la llamada al repositorio.
    }
  }
}

export default ElectorService;
```

