#### Esta es una rama del proyecto original, como el repositorio esta en mi github no puedo hacer fork.
# Estilos de Programación
## 1. Hooks
Los **hooks** son una característica importante de React que permite usar el estado y otras características de React en componentes funcionales sin tener que escribir clases. En Next.js, también puedes usar hooks para mantener el estado y la lógica del componente.

### /pages/login

```javascript
const [credentials, setCredentials] = useState({
  username: '',
  password: '',
});

const handleChange = (e) => {
  setCredentials({
    ...credentials,
    [e.target.name]: e.target.value,
  });
};
```
### 2. Composición de Componentes
Next.js promueve la composición de **componentes**, lo que permite construir componentes más complejos a partir de componentes más pequeños y **reutilizables**.

### /componentes/Layout.js
```javascript
return (
  <div>
    {/*...*/}
    <div>
      <div>Username: {username}</div>
    </div>
    <Verify {/*Como vemos esta etiqueta <Verify /> es un componente reutilizable */} 
      isOpen={isOpen}
      onRequestClose={() => setIsOpen(false)}
      onConfirm={logout}
      text={"Estas seguro de cerrar sesion?"}
    />
    {/*...*/}
  </div>
);
```
### /componentes/Verify.js
Componente que genera una ventana modal para verificar la desicion de un usuario
```javascript
return (
    {/*...*/}
      <div className="modal-header">
        <h5 className="modal-title">{text}</h5>
        <button type="button" className="close" onClick={onRequestClose}>
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div className="modal-footer">
        <button type="button" className="btn btn-primary" onClick={onConfirm}>
          Sí
        </button>
        <button type="button" className="btn btn-secondary" onClick={onRequestClose}>
          No
        </button>
      </div>
    {/*...*/}
);
```
### 3.Fetching Data on the Client Side
En este enfoque, se realiza la solicitud a la API directamente desde el cliente (el navegador) utilizando JavaScript, generalmente con la ayuda de la función fetch() o 
mediante librerías como **axios**.
Este estilo se puede implementar dentro de componentes de función utilizando el hook useEffect para realizar la llamada a la API y **actualizar** el **estado** del 
componente con los datos recibidos

### /componentes/Layout.js
```javascript
const [username, setUsername] = useState(0);
  useEffect(() => {
    getProfile();
  }, []);
  const getProfile = async () => {
    const res = await axios.get("/api/profile");
    setUsername(res.data.username);
  };
```
### 4. Gestión de Sesiones con Cookies y JWT o "Session Management with Cookies and JWT" en inglés.
4.1 Uso de Cookies: El código utiliza la librería cookie para acceder a las cookies enviadas en la solicitud (req.cookies) y también para crear una cookie de logout al establecerla en la respuesta (res.setHeader("Set-Cookie", serialized)). Las cookies se utilizan para almacenar el token de autenticación y para eliminar la cookie al cerrar la sesión.
4.2 Uso de JWT (JSON Web Tokens): Se utiliza la función verify de la librería jsonwebtoken para verificar la validez del token de autenticación almacenado en la cookie MyTokenName. Si el token es válido, se procede a eliminar la cookie y cerrar la sesión.
4.3 Gestión de Sesiones: La función es responsable de gestionar la sesión del usuario. Si el token de autenticación no está presente o no es válido, se responde con un error (401 - Unauthorized) indicando que no se encuentra un token válido o que el usuario no está autenticado.

### /pages/api/services/login.js
Generar Token (iniciar sesion)
```javascript
if(username == userValidate.username && password == userValidate.password){
  const token = jwt.sign(
    {
      exp: expirationTime,
      id: userValidate.id,
      username: userValidate.username,
      userType: userValidate.userType,
    },
    "secret"
  );
    const serialized = serialize("MyTokenName", token, {
    httpOnly: true,
    secure: process.env.NODE_ENV === "production",
    sameSite: "strict",
    maxAge: expirationTime, // tiempo del token
    path: "/",
  });
  res.setHeader("Set-Cookie", serialized);
  return res.status(200).json({ message: "Login successfully", userType: userValidate.userType  });
}
```
Finalizar Token (cerrar sesion)
```javascript
    verify(MyTokenName, "secret");
    const serialized = serialize("MyTokenName", null, {
      httpOnly: true,
      secure: process.env.NODE_ENV === "production",
      sameSite: "strict",
      maxAge: 0,
      path: "/",
    });
    res.setHeader("Set-Cookie", serialized);
    return res.status(200).json("Logout Succesfully");
```
### 5. Clases y Métodos Estáticos
En este estilo, se utiliza una clase para encapsular funcionalidades relacionadas en métodos estáticos que pueden ser invocados sin necesidad de crear una instancia de la clase.
Uso de Clases: La clase UserRepository encapsula métodos relacionados con el acceso a la base de datos, en este caso, obtener usuarios.
### /data/repositorio/PersonRepository.js
```javascript
import {pool} from "@/ldavis/data/config/db";
import Elector from "@/ldavis/domain/models/Elector";
import Admin from "@/ldavis/domain/models/Admin";
class PersonRepository {
    static async getPerson(username,password) {
        try{

            const [result] = await pool.query(
                "SELECT * FROM persona WHERE username = ? AND password = ?",
                [username, password]
            );
            console.log(result);
            let person;
            if (result.length > 0) {
                const [admin] = await pool.query("SELECT * FROM administrador WHERE id = ?", result[0].id);
                if (admin.length === 0) {
                    person = new Elector(result[0].id, result[0].name, result[0].lastName, result[0].username, "ldavis@unsa.edu.pe");
                }else{
                    person = new Admin(result[0].id, result[0].name, result[0].lastName, result[0].username, "Gerente de Sistemas");
                }
                return {status:200,person}
            }
            else{
                return {status:401}
            }
        }catch (error){
            return error;
        }
    }
}
export default PersonRepository;

```

# Codificación Legible (Clean Code)
### 1. Organización del código:
El código está organizado en bloques lógicos utilizando funciones y separando los elementos relacionados. Las funciones handleChange y handleSubmit están separadas para manejar diferentes aspectos de la lógica.
### /pages/login.js
```javascript
  const [credentials, setCredentials] = useState({
    username: '',
    password: '',
  });

  const handleChange = (e) => {
    setCredentials({
      ...credentials,
      [e.target.name]: e.target.value,
    });
  };
/* ... codigo ... */
```
### 2. Manejo de errores
Se utiliza un bloque try-catch para manejar errores potenciales al hacer la solicitud a la API. Los errores se registran en la consola para facilitar el diagnóstico.
### /pages/login.js
```javascript
try{
  const [result] = await pool.query(
    "SELECT * FROM persona WHERE username = ? AND password = ?",
      [username, password]
    );
  console.log(result);
  /* ... codigo ... */
  }catch (error){
    return error;
  }
```
### 3. Configuración de Cookies Seguras
Al configurar la cookie con la función serialize de la librería cookie, se establecen opciones de seguridad como httpOnly, secure, y sameSite. Estas opciones ayudan a proteger la cookie contra ataques como el acceso desde scripts no autorizados (httpOnly), aseguran que la cookie solo se envíe sobre conexiones seguras (secure), y controlan cuándo se envía la cookie al servidor (sameSite). Estas configuraciones mejoran la seguridad de la autenticación y previenen ataques como el robo de sesión.
### /pages/api/services/login.js
```javascript
            
            const serialized = serialize("MyTokenName", token, {
                httpOnly: true,
                secure: process.env.NODE_ENV === "production",
                sameSite: "strict",
                maxAge: expirationTime, // tiempo del token
                path: "/",
            });
/* ... codigo ... */
```
### 4. Uso de Fragmentos <></>
El componente Layout utiliza un fragmento (<></>) para envolver los elementos del JSX, lo que permite devolver varios elementos sin agregar un nodo adicional al DOM.
### /pages/index.js

```javascript
import Link from 'next/link';
export default function IndexPage() {
  return (
      <>
          <div className="mt-3">
            <Link href="/login">Iniciar sesion</Link>
          </div>
      </>
  );
}
```
### 5. Identacion y espaciado
El código tiene una indentación y un espaciado consistentes, lo que mejora la legibilidad y facilita el seguimiento de la estructura del código.
### En todos los archivos.js del proyecto

```javascript

  return (
      <div className="container mt-5">
        <form>
          <div className="form-group">
            <label htmlFor="username">Username</label>
            <input
                name="username"
                type="text"
                className="form-control"
                id="username"
                placeholder="Enter username"
                onChange={handleChange}
                value={credentials.username}
            />
          </div>
          <div className="form-group">
            <label htmlFor="password">Password</label>
            <input
                name="password"
                type="password"
                className="form-control"
                id="password"
                placeholder="Enter password"
                onChange={handleChange}
                value={credentials.password}
            />
          </div>
          <button type="submit" className="btn btn-primary" onClick={handleSubmit}>
            Login
          </button>
        </form>
        <Link href={"/"}>Home</Link>
      </div>
  );
/* ... codigo ... */
}
```
# Principios SOLID
### 1. Responsabilidad Única (SRP - Single Responsibility Principle):
Este principio establece que una clase o módulo debe tener una única responsabilidad o motivo para cambiar. En otras palabras, una clase debe tener una única razón para ser modificada. Si una clase tiene más de una responsabilidad, es más propensa a cambios y se vuelve difícil de mantener.
La función **loginHandle** tiene una única responsabilidad: manejar la lógica de inicio de sesión y generar el token de autenticación.
### pages/login.js
```javascript
  export default async function loginHandle(req, res) {
    const { username, password } = req.body;
    const result = await AuthService.authenticate(username, password);
    console.log(result)
    if (result.status === 200) {
 /* ... codigo ... */
```
### 2. Separación de Intereses (SoC - Separation of Concerns):
Es un principio de diseño de software que aboga por dividir un programa en componentes o módulos más pequeños, cada uno enfocado en resolver una única preocupación o responsabilidad.
El código se estructura en bloques bien definidos y separados. Por ejemplo, separamos la lógica de obtención de perfil (getProfile) y la lógica de cierre de sesión (logout) en funciones independientes.
### components/Layout.js
```javascript
 const getProfile = async () => {
     /* ... codigo ... */
  };
  const logout = async () => {
     /* ... codigo ... */
  };
```
### 3. Principio de Inversión de Dependencia (DIP - Dependency Inversion Principle):
El componente Layout hace uso de la inyección de dependencias para obtener los datos de perfil del usuario. En lugar de tener una dependencia directa de axios y realizar la llamada HTTP directamente en el componente, se pasa una función (getProfile) que maneja la llamada HTTP como una dependencia. Esto permite que el componente sea más flexible y reutilizable, y facilita la prueba a través de la inyección de dependencias.
## components/Layout.js
```javascript

const getProfile = async () => {
    const res = await axios.get("/api/profile");
    setUsername(res.data.username);
  };
```
