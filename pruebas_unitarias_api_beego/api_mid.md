# Api mid
En esta sección se mostrará como consolidar adecuadamente el Api mid

## Orientacion (teoria)
las api_mid están orientados a interoperar con otras apis y consolidar información, separando todo lo orientado al crud en las respectivas api_crud y todo lo del modelo de negocio en estas. Por ende, es apropiado **establecer al maximo funciones dedicadas** que luego seran expuesta en los servicios del mid.

## Ejemplo

Se desea Crear un servicio que ingresando un id, retorna este id y un saludo.

Lo más normal para esta solicitud es crear un servicio y en este crear el saludo

      // Saludo ...
      // @Title Saludo
      // @Description get Saludo by id
      // @Param	id		path 	string	true		"The key for staticblock"
      // @Success 200 {object} models
      // @Failure 404 not found resource
      // @router saludo/:id [get]
      func (c *UsuarioController) Saludo() {
      	idStr := c.Ctx.Input.Param(":id")
      	id, _ := strconv.Atoi(idStr)
      	c.Data["json"] = map[string]interface{}{"Code": id, "Body": "hola que hace"}
      	c.ServeJSON()
      }

pero qué pasa si queremos extender las funcionalidades del saludo, o queremos implementar en otra servicio el saludo. No vale la pena replicar código, para esto se desarrolla una función que se encargue de generar el saludo y al controlador solo lo dejamos como el puente que expone la información.

    // Saludo ...
    // @Title Saludo
    // @Description get Saludo by id
    // @Param	id		path 	string	true		"The key for staticblock"
    // @Success 200 {object} models
    // @Failure 404 not found resource
    // @router saludo/:id [get]
    func (c *UsuarioController) Saludo() {
    	idStr := c.Ctx.Input.Param(":id")
    	id, _ := strconv.Atoi(idStr)
    	c.Data["json"] = map[string]interface{}{"Code": id, "Body": GeneradorSaludo()}
    	c.ServeJSON()
    }

    // GeneradorSaludo ...
    func GeneradorSaludo() (result string) {
    	result = "Hola a todos"
    	return
    }