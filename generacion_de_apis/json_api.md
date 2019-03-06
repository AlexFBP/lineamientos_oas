# Configurar Respuestas Json en API Beego

En está sección se especificarán los ajustes pertinentes para que las API creadas en el framewor Beego respondan en JSON conforme a los estandares de la Oficina Asesora de Sistemas; esto con el fin de que no genere problemas al intregarse con el administrador de servicios WSO2.

## Refactoring Controllers

Editar el **main.go** de la API a Ajustar. Agregar las plantillas de errores que se encuentran en el repositorio **[utils_oas](https://github.com/udistrital/utils_oas)** de la siguiente forma.

- Importar paquete:

      import (
        "github.com/udistrital/utils_oas/customerror"
      )

- Implementación en **func main()**:

      beego.ErrorController(&customerror.CustomErrorController{})

- El **main.go** Lucirá de la siguiente forma:

      package main

      import (
          "github.com/astaxie/beego"
          "github.com/astaxie/beego/orm"
          "github.com/astaxie/beego/plugins/cors"
          _ "github.com/jotavargas/debug_beego_request/routers"
          _ "github.com/lib/pq"
          "github.com/udistrital/utils_oas/customerror"
      )

      func init() {
          orm.RegisterDataBase("default", "postgres", "postgres://postgres:postgres@127.0.0.1/test?sslmode=disable")
      }

      func main() {
          if beego.BConfig.RunMode == "dev" {
              beego.BConfig.WebConfig.DirectoryIndex = true
              beego.BConfig.WebConfig.StaticDir["/swagger"] = "swagger"
          }

          beego.InsertFilter("*", beego.BeforeRouter, cors.Allow(&cors.Options{
              AllowOrigins: []string{"*"},
              AllowMethods: []string{"PUT", "PATCH", "GET", "POST", "OPTIONS", "DELETE"},
              AllowHeaders: []string{"Origin", "x-requested-with",
                  "content-type",
                  "accept",
                  "origin",
                  "authorization",
                  "x-csrftoken"},
              ExposeHeaders:    []string{"Content-Length"},
              AllowCredentials: true,
          }))
          beego.ErrorController(&customerror.CustomErrorController{})
          beego.Run()
      }

## Script para Refactor

Se desarrolló un script en python 2.7 para realizar los ajustes de los micro servicios desarrolladon en el framework Beego de forma masiva y automática. la única restricción que existe, es que **solo realiza los ajustes en micro servicios que nos se han personalizado o modificado en sus líneas**. [refactor_controller](https://github.com/jotavargas/refactor_controller)

- Clonar script para refactor.

        git clone https://github.com/jotavargas/refactor_controller.git

- Ejecutar script

      #ir al proyecto
      cd refactor_controller

      #como ejecutar (con python 2.7)
      python main.py -F ruta_controladores_del_api_a_refactoring

      #Ejemplo:
      python main.py -F /home/jjvargass/go/src/github.com/udistrital/api_financiera/controllers

- Indentar Controladores *.go

      cd ruta_controladores_del_api_a_refactoring
      gofmt -w *.go



Los cambios específicos en cada uno de los microservicios se definirán a continuación.


## Solicitud POST

  ![Refactor Metodo Post](/generacion_de_apis/img/post.png)


## Solicitud GETONE

  ![Refactor Metodo GetOne](/generacion_de_apis/img/getone.png)


## Solicitud GETALL

  ![Refactor Metodo GetAll](/generacion_de_apis/img/getall1.png)

  ![Refactor Metodo GetAll](/generacion_de_apis/img/getall2.png)



### Solicitud PUT

  ![Refactor Metodo Post](/generacion_de_apis/img/put.png)


### DELETE

  ![Refactor Metodo Post](/generacion_de_apis/img/delete.png)
