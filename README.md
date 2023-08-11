## Trabajo encargado:
```bash
- data/repository/
      -CandidateRepositoryjs
      -ElectorRepository.js
      -PersonRepository.js
      -ResultRepository.js
- styles/
- components/
      -Verify.js
      -Success.js
```

## 1. CandidateRepository (data/repository/CandidateRepository.js)
### Estilo de Programación: (Arrays)
Este fragmento de código hace uso de arrays para procesar y organizar datos obtenidos de una base de datos. A través de la función map, se itera sobre los resultados obtenidos y se construyen objetos Candidate con sus respectivas propiedades. Estos objetos se almacenan en un nuevo array listCandidate, facilitando la manipulación y presentación de los datos. El enfoque en el uso de arrays permite una estructura ordenada y eficiente para la gestión de los registros de candidatos, contribuyendo a la legibilidad y mantenibilidad del código.
### Convenciones de programación aplicados:

    - Commenting & Documentation: No hay comentarios ni documentación que expliquen el propósito o funcionamiento del código.
    - Consistent Indentation: El código muestra una indentación consistente, con bloques de código anidados correctamente.
    - Consistent Naming Scheme: Se siguen convenciones de nombres consistentes para las funciones y variables, comogetCandidate(), listCandidate, newCandidate, etc.
    - Separation of Code and Data: El código realiza operaciones en la base de datos y crea instancias de objetos Candidate, lo  que muestra una separación de lógica de negocio y datos.
    - Object-Oriented vs. Procedural: El código utiliza programación orientada a objetos al crear instancias de la clase Candidate y manipular los resultados obtenidos de la base de datos.
    
```javascript
import {pool} from "@/ldavis/data/config/db";
import Candidate from "@/ldavis/domain/models/Candidate";
class CandidateRepository {
    static async getCandidate() {
        try{
            const [result] = await pool.query("CALL obtener_candidatos_por_partido();");
            const listCandidate = [];
            result[0].map(e=>{
                const newCandidate = new Candidate(e.id,e.name,e.lastName,e.username,e.cargo,e.nombre_partido);
                listCandidate.push(newCandidate);
            })
            return listCandidate;
        }
        catch (error){
            return error;
        }
    }
}

export default CandidateRepository;
```
**Single Responsibility Principle (SRP):** La clase CandidateRepository tiene una única responsabilidad: interactuar con la base de datos para obtener candidatos. Su función principal es recuperar y construir instancias de objetos Candidate, manteniendo un único propósito.

## 2. ElectorRepository (data/repository/ElectorRepository.js)
### Estilo de Programación (Quarantine)
El estilo Quarantine se utiliza en el código del repositorio ElectorRepository para describir la separación y aislamiento intencional de las operaciones de base de datos. Este enfoque garantiza que las interacciones con la base de datos estén confinadas en una ubicación específica y se gestionen de manera aislada del resto del sistema. Al aislar estas operaciones en su propia clase, se mejora la organización y mantenibilidad del código, al tiempo que se reduce el riesgo de que las interacciones con la base de datos afecten inadvertidamente otras partes del sistema. Este enfoque se alinea con las prácticas de diseño que buscan limitar el impacto de los cambios y errores potenciales en las interacciones con bases de datos, lo que contribuye a un código más ordenado y robusto en la gestión de datos.
### Convenciones de programación aplicados:
    - Commenting & Documentation: El código no tiene comentarios o documentación que expliquen el propósito y funcionamiento de las funciones y operaciones realizadas.
    - Consistent Indentation: El código muestra una indentación consistente, con bloques de código anidados correctamente.
    - Consistent Naming Scheme: Se siguen convenciones de nombres consistentes para las funciones y variables, como saveVote(), getPoliticalParty(), listPoliticalParty, newPoliticalParty, etc.
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
**Single Responsibility Principle (SRP):** La clase ElectorRepository se ocupa de manejar la lógica de guardar votos y obtener partidos políticos. Cada método se enfoca en una única responsabilidad, que es manipular la base de datos en relación con la votación y los partidos políticos.
## 3. CandidateRepository (data/repository/CandidateRepository.js)
### Estilo de Programación: (Holywood)
El patrón de diseño Hollywood es empleado en el código para la gestión de personas (PersonRepository) en el contexto de autenticación. En este enfoque, el componente de alto nivel (PersonRepository) toma el control y llama a los componentes de bajo nivel (Electror y Admin) según sea necesario. Esto evita la dependencia directa de los componentes de bajo nivel en los de alto nivel, promoviendo una estructura más organizada y desacoplada. El componente de alto nivel orquesta la creación de instancias de Electror y Admin basándose en los resultados de la consulta a la base de datos. Al separar la lógica de control de las decisiones sobre la creación de objetos, se mejora la modularidad y facilita futuras expansiones o cambios en la lógica. Este enfoque se asemeja a cómo en Hollywood los actores son llamados por el director para desempeñar sus roles, evitando así que los actores tomen decisiones sobre cuándo o cómo son requeridos en la producción.
### Convenciones de programación aplicados:
    - Commenting & Documentation: El código no tiene comentarios o documentación que expliquen el propósito y funcionamiento de las funciones y operaciones realizadas.
    - Consistent Indentation: El código muestra una indentación consistente, con bloques de código anidados correctamente.
    - Consistent Naming Scheme: Se siguen convenciones de nombres consistentes para las funciones, variables y clases, como getPerson(), Elector, Admin, etc.
    - Separation of Code and Data: El código realiza operaciones en la base de datos y crea instancias de objetos Elector y Admin, lo que muestra una separación de lógica de negocio y datos.
```javascript
import {pool} from "@/ldavis/data/config/db";
import Elector from "@/ldavis/domain/models/Elector";
import Admin from "@/ldavis/domain/models/Admin";

class PersonRepository {
    static async getPerson(username, password) {
        try {
            const [result] = await pool.query(
                "SELECT * FROM persona WHERE username = ? AND password = ?",
                [username, password]
            );

            let person;
            if (result.length > 0) {
                const [admin] = await pool.query("SELECT * FROM administrador WHERE id = ?", result[0].id);
                if (admin.length === 0) {
                    person = new Elector(result[0].id, result[0].name, result[0].lastName, result[0].username, "ldavis@unsa.edu.pe");
                } else {
                    person = new Admin(result[0].id, result[0].name, result[0].lastName, result[0].username, "Gerente de Sistemas");
                }
                return {status: 200, person};
            } else {
                return {status: 401};
            }
        } catch (error) {
            return error;
        }
    }
}

export default PersonRepository;

```
**Single Responsibility Principle (SRP):** La clase PersonRepository tiene la responsabilidad de autenticar y obtener información sobre personas. Aunque no está explícitamente separado en diferentes clases, cada método se enfoca en una tarea específica.
## 4. ResultRepository (data/repository/ResultRepository.js)
### Estilo de Programación:(Arrays)
Este código utiliza el estilo de programación Arrays al emplear de manera efectiva la manipulación de arrays para gestionar datos. En específico, se obtienen resultados de una base de datos y se transforman en instancias de una clase ResultVote. Mediante el uso del método map, se recorre el conjunto de resultados y se crea un nuevo array que almacena estas instancias. Este enfoque de programación orientada a arrays permite un procesamiento organizado y eficiente de datos, lo que mejora la claridad y mantenibilidad del código.
### Convenciones de programación aplicados:
    - Commenting & Documentation: El código no tiene comentarios o documentación que expliquen el propósito y funcionamiento de las funciones y operaciones realizadas.
    - Consistent Indentation: El código muestra una indentación consistente, con bloques de código anidados correctamente.
    - Consistent Naming Scheme: Se siguen convenciones de nombres consistentes para las funciones, variables y clases, como getResult(), ResultVote, etc.
    - Separation of Code and Data: El código realiza operaciones en la base de datos y crea instancias de objetos ResultVote, lo que muestra una separación de lógica de negocio y datos.
```javascript
import {pool} from "@/ldavis/data/config/db";
import ResultVote from "@/ldavis/domain/models/ResultVote";
class ResultRepository{
    static async getResult(){
        try{
            const [result] = await pool.query("CALL sp_obtener_datos_partido_candidatos();");
            const listResults = [];
            result[0].map(e=>{
                const newPoliticalParty = new ResultVote(e.id_partido,e.nombre_partido,e.name + " " + e.lastName,e.nro_votos);
                listResults.push(newPoliticalParty);
            })
            return listResults;
        } catch (error){
            return error
        }
    }
}
export default ResultRepository;

```  
**Single Responsibility Principle (SRP):** La clase ResultRepository se encarga de obtener resultados de votación a partir de la base de datos y construir instancias de objetos ResultVote. Cada método se enfoca en una única responsabilidad.
