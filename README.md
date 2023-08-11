This is a [Next.js](https://nextjs.org/) project bootstrapped with [`create-next-app`](https://github.com/vercel/next.js/tree/canary/packages/create-next-app).

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
### Names rules 
Uso de camelCase: (pages/votacion.js)
```javascript

  // Estados
  const [partidos, setPartidos] = useState([]);
  const [isOpen, setIsOpen] = useState(false);
  const [isSuccess, setIsSuccess] = useState(false);
  const [success, setSuccess] = useState(false);
  const [vote, setVote] = useState({
    idElector: 0,
    idPoliticalParty: 0,
    date: new Date(),
  });
```
### Manejo de Errores 
Manejo de excepeciones a través de try y catch : (services/VoteElector.js)
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
### Limit Line Length
Linea de codigo menor que 80 caracteres (pages/votacion.js)
```javascript
     <Success show={isSuccess} handleClose={logout} isSuccess={success} />
     <Verify
          isOpen={isOpen}
          onRequestClose={() => setIsOpen(false)}
          onConfirm={handleSubmit}
          text={"Estas seguro de tu voto?"}
     />
     <button onClick={(e) => { e.preventDefault(); setIsOpen(true); }}>
          Votar
     </button>
```
### Comments rules
Comentarios (pages/api/voteElector/vote.js)
```javascript
// Función para manejar una solicitud de voto
export default async function handleVote(req, res) {
  const vote = req.body; // Datos del voto desde el cuerpo de la solicitud

  try {
    // Intentamos enviar el voto al servicio VoteElector para su procesamiento
    const result = await VoteElector.sendVote(vote);

    // Verificamos el estado de la respuesta para dar la respuesta adecuada
    if (result.status === 200) {
      return res.status(200).json({ message: "Voto correcto" });
    } else if (result.status === 401) {
      return res.status(401).json({ message: "Usuario ya votó" });
    }

    // Si no se cumplieron las condiciones anteriores, respondemos con un error 500
    return res.status(500).json({ message: "Ocurrió un error" });
  } catch (err) {
    // Si ocurre algún error durante el proceso, lo capturamos y respondemos con un error 500
    res.status(500).json({ error: err }); 
  }
}
```
### Functions rules
La regla de funciones en Clean Code promueve funciones pequeñas, cohesivas y con nombres descriptivos que hagan una sola cosa sin efectos secundarios. Se debe evitar el uso de argumentos de bandera y, en su lugar, dividir funciones en métodos más pequeños y especializados para mantener el código limpio y legible. Esto conduce a un código más claro, modular y fácil de mantener.

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

