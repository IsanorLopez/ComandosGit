

Use MaestroMuebles
Go

ALTER PROCEDURE dbo.proc_grabarPolizaPromociones @iDepto tinyint,@sInicialesDeptoDiv varchar(3),@iFolio int,@iSubFolio int,@iSubSubFolio int,
                                                  @iProveedor int,@iIva tinyint,@iImporteSinIva bigint,@iImporteDelIva bigint,@iEmpleado int,
                                                  @iFormaCobro tinyint,@cIdentificador varchar(35),@sDescConcepto varchar(150), @sFechaTipoPromocion varchar(100), @sFechaPromocion varchar(50)
with execute as owner  
AS
begin     
/*****************************************************************************  
--DROP PROCEDURE proc_grabarPolizaPromociones  
EXEC proc_grabarPolizaPromociones  
FECHA:      27/05/2015.  
DESCRIPCION: genera la poliza de promociones
  
MODIFICO: 	  Isanor Lopez
SOLICITO: 	  Rafael Ramirez Mojardin
FECHA:  	  11/02/2020
DESCRIPCION:  Se modifica para generar multiples polizas
*****************************************************************************/ 

set nocount on
/*
declare @iDepto tinyint,@sInicialesDeptoDiv varchar(3),@iProveedor int,@iIva tinyint,@iImporteSinIva bigint,@iImporteDelIva bigint,@iEmpleado int,@iFormaCobro tinyint,@cIdentificador varchar(35)
declare @iFolio int,@iSubFolio int,@iSubSubFolio int

set @iFolio=1532
set @iSubFolio=1
set @iSubSubFolio=0

set @iDepto=1
set @sInicialesDeptoDiv='D'
set @iProveedor=10006
set @iIva=16
set @iImporteSinIva=-1058830
set @iImporteDelIva=-169413
set @iEmpleado=90031984
set @iFormaCobro=1
set @cIdentificador=''
*/
declare @dFechaPago smalldatetime,
		@sSql nvarchar(4000),
		@iFolioPoliza int,
		@sTmpSolicitarFactura varchar (150),
		@iClv_tipoFactura int,
		@sTipoPromocion varchar (5),
		@sConceptoDespacho varchar(150)

		set @sTmpSolicitarFactura = 'temporales.dbo.tmpSolicitarFactura'+cast(@iEmpleado as varchar)+'3'

	if object_id('tempdb.dbo.#tmpcomprasgeneral', 'U') is not null
		drop table #tmpcomprasgeneral	
		
	create table #tmpcomprasgeneral
	(
		poliza int not null default(0),
		fecha smalldatetime not null default(getdate()),
		NumProveedor  int not null default(0),
		NacOImp varchar(1) not null default(''),
		TipoPoliza tinyint not null default(0),
		NumEmpleado int not null default(0),		
        opc_chequeodecompetencia int not null default (0)
	)
	
	if object_id('tempdb.dbo.#tmpcompraspolizas', 'U') is not null
		drop table #tmpcompraspolizas	
		
	create table #tmpcompraspolizas
	(
		Poliza int not null default(0),
		Cuenta int not null default(0),
		SubCuenta int not null default(0),
		Centro int not null default(0),
		Descripcion varchar(60) not null default(''),
		CargoOAbono varchar(1) not null default(''),
		FechaFactura smalldatetime not null default(getdate()),		
		Importe	bigint not null default(0),
		Factura	varchar(40) not null default(''),
		Letra varchar(10) not null default(''),
		ImporteSinIva bigint not null default(0),
		ImporteDelIva bigint not null default(0),
	)
	
	if object_id('tempdb.dbo.#tmpPagosBonifSinSku', 'U') is not null
		drop table #tmpPagosBonifSinSku	
				
	 create table #tmpPagosBonifSinSku
    ( 
       Folio varchar(100) not null default(0),
	   SubFolio int not null default(0),
	   SubSubFolio int not null default(0),
       FechaFactura smalldatetime not null default('1900-01-01'), 
       NumPoliza int not null default(0),
	   NumProveedor int not null default(0),
       Importe bigint not null default(0),  
       Concepto varchar(150) not null default(' '), 
       Descripcion  varchar(65) not null default(' ') 
    ) 
    
    if object_id('tempdb.dbo.#tmpCodigosPolizaPromo', 'U') is not null
		drop table #tmpCodigosPolizaPromo	
		
    create table #tmpCodigosPolizaPromo
	( 
		NumPoliza int not null default(0),
		Codigo int not null default(0), 
		ImporteNeto bigint not null default(0), 
		Cantidad int not null default(0), 
		CostoSalida bigint not null default(0), 
		CostoEntrada bigint not null default(0) 
	)
	
	if object_id('tempdb.dbo.#tmpInfoPolizas', 'U') is not null
	drop table #tmpInfoPolizas	
		
    create table #tmpInfoPolizas
	( 
		NumPoliza int not null default(0),
		NumPolizaNueva bigint not null default(0),
		NumProveedor int not null default(0),
		NumConsecutivo bigint not null default(0),
		NumFactura varchar(40) not null default '',
		opc_chequeodecompetencia bit not null default 0,
		iImporteSinIva bigint not null default (0),
		iImporteDelIva bigint not null default (0),
		leido int not null default(0)
	)
		
    set @dFechaPago=cast(GETDATE() as date)
            
    --se pasa la info de los pagos  a tabla #
    set @sSql=
    'insert into #tmpPagosBonifSinSku(Importe,concepto,Folio,SubFolio,SubSubFolio,FechaFactura,NumPoliza,NumProveedor) '+
    'select Importe,concepto,Folio,SubFolio,SubSubFolio,FechaFactura,NumPoliza,NumProveedor from temporales.dbo.tmpPagosBonifSinSku'+@cIdentificador+' '
	exec MaestroMuebles.dbo.sp_executesql @sSql
	
	--se pasa la info de los codigos a tabla #
	set @sSql=
	'insert into #tmpCodigosPolizaPromo(Codigo,ImporteNeto,Cantidad,CostoSalida,CostoEntrada,NumPoliza) '+
	'select Codigo,ImporteNeto,Cantidad,CostoSalida,CostoEntrada,NumPoliza from temporales.dbo.tmpCodigosPolizaPromo'+@cIdentificador+' '
	exec MaestroMuebles.dbo.sp_executesql @sSql
	
	--se obtiene todas las polizas necesarias #
	set @sSql=
	'insert into #tmpInfoPolizas(NumPoliza,NumProveedor,NumConsecutivo) '+
	'select distinct NumPoliza,NumProveedor, ROW_NUMBER() OVER(PARTITION BY NumProveedor ORDER BY NumPoliza ASC) from temporales.dbo.tmpPagosBonifSinSku'+@cIdentificador+' '
	exec MaestroMuebles.dbo.sp_executesql @sSql

	--se obtiene la factura y letra
	declare @sFactura varchar(40),
			@sLetra varchar(10)

	set @sFactura=''
	set @sLetra='MUEB'
	
	-- Se calculan los importes, con iva y sin iva
	update #tmpInfoPolizas
	set iImporteSinIva = b.importeSinIva,
		iImporteDelIva = b.importeSinIva * @iIva
	from #tmpInfoPolizas as a
	join (select NumPoliza, SUM(a.ImporteNeto) as importeSinIva
		  from #tmpCodigosPolizaPromo as a 
		  group by a.NumPoliza
		  ) as b
	on a.NumPoliza = b.Numpoliza

	-- Se obtiene la factura por proveedor
	update #tmpInfoPolizas 
	set NumFactura =  cast((b.ConsecutivoFactura + a.NumConsecutivo) as varchar(40))
	from #tmpInfoPolizas as a join maestromuebles.dbo.cenproveedores as b
	on a.NumProveedor = b.NumProveedor

	update maestromuebles.dbo.cenproveedores
	set ConsecutivoFactura= a.ConsecutivoFactura + b.NumConsecutivo 
	from maestromuebles.dbo.cenproveedores as a join (select NumProveedor, MAX(NumConsecutivo) as NumConsecutivo 
													  from #tmpInfoPolizas 
													  group by NumProveedor) as b
	on a.NumProveedor = b.NumProveedor 

SET XACT_ABORT ON;    
	begin transaction tranFoliador
	
	declare @iConsecutivo int = 0
     --consigue las polizas a generar ,se genera por aÃ±o-iva-proveedor-consecutivo  (cada 10 facturas es poliza nva..)
	while exists(select top 1 NumPoliza from #tmpInfoPolizas where leido=0)
	
	begin
	
        select top 1 @iConsecutivo= NumPoliza from #tmpInfoPolizas where leido=0 order by NumPoliza asc

		Exec maestromuebles.dbo.SP_BmFoliador_02 2,'POLIZAS','FOLIO PARA LAS POLIZAS, NACIONAL, IMPORTACION, CANCELACION, BONIFICACIONES CON Y SIN CODIGO', @iFolioPoliza OutPut

        update #tmpInfoPolizas set NumPolizaNueva=@iFolioPoliza, leido=1 where NumPoliza=@iConsecutivo           
	end
	
	commit transaction tranFoliador
	
	update #tmpInfoPolizas
	set opc_chequeodecompetencia = c.opc_chequeodecompetencia 
	from #tmpInfoPolizas as a
	join #tmpPagosBonifSinSku as b 
	on a.NumPoliza = b.NumPoliza
	join ComprasMuebles.dbo.ctl_comcobrosproveedordetalle as c with(nolock)
	on b.Folio = c.num_folio and b.SubFolio = c.num_subfolio and b.SubSubFolio = c.num_subsubfolio
		
	insert into #tmpcomprasgeneral(Poliza,Fecha,NumProveedor,NacOImp,TipoPoliza,NumEmpleado,opc_chequeodecompetencia)
	select a.NumPolizaNueva,getdate(),a.NumProveedor,b.NacOImp,9,@iEmpleado, a.opc_chequeodecompetencia
	from #tmpInfoPolizas as a  
	join maestromuebles.dbo.CenProveedores as b with(nolock)
	on a.NumProveedor = b.NumProveedor
	
	insert into #tmpcompraspolizas(Poliza,Cuenta,SubCuenta,Centro,Descripcion,CargoOAbono,FechaFactura)
	select NumPolizaNueva,1661,1023,205101,'INVENTARIOS MUEBLES NUEVOS','C',@dFechaPago 
	from #tmpInfoPolizas

	--2119
	insert into #tmpcompraspolizas(Poliza,Cuenta,SubCuenta,Centro,Descripcion,CargoOAbono,Importe,Factura,Letra,FechaFactura,ImporteSinIva,ImporteDelIva)
	select a.NumPolizaNueva,b.cuentaprov,a.NumProveedor,c.Num_SSubCuenta,d.RazonSocial,'A',a.iImporteSinIva + a.iImporteDelIva,@sFactura,@sLetra,@dFechaPago,a.iImporteSinIva,a.iImporteDelIva
	from #tmpInfoPolizas as a
	join maestromuebles.dbo.CenProveedores as d with(nolock)
	on a.NumProveedor = d.NumProveedor
	cross join (select cuentaprov from maestromuebles.dbo.CenComprasIvas (nolock) where AAAA=YEAR(GETDATE()) and IVA=@iIva) b
	cross join (select distinct Num_SSubCuenta from maestromuebles.dbo.CenDeptosDivision (nolock) where Depto=@iDepto and InicialesDeptoDiv=@sInicialesDeptoDiv) c
	
	--IVA diferido
	insert into #tmpcompraspolizas(Poliza,Cuenta,SubCuenta,Centro,Descripcion,CargoOAbono,Importe,FechaFactura,ImporteSinIva,ImporteDelIva)
	select a.NumPolizaNueva,b.cuentaiva,b.subcuenta,b.SSubCuenta,b.nombre,'C',a.iImporteDelIva,@dFechaPago,0,0
	from #tmpInfoPolizas as a 
	cross join (select cuentaiva,subcuenta,SSubCuenta,nombre from maestromuebles.dbo.CenComprasIvas (nolock) where AAAA=YEAR(GETDATE()) and IVA=@iIva) b
	cross join (select distinct Num_SSubCuenta from maestromuebles.dbo.CenDeptosDivision (nolock) where Depto=@iDepto and InicialesDeptoDiv=@sInicialesDeptoDiv) c
	where a.iImporteDelIva <> 0
		
	--4601
	insert into #tmpcompraspolizas(Poliza,Cuenta,SubCuenta,Centro,Descripcion,CargoOAbono,Importe,Factura,Letra,FechaFactura,ImporteSinIva,ImporteDelIva)
	select a.NumPolizaNueva,c.Num_Cuenta,c.Num_SubCuenta,c.Num_SSubCuenta,d.RazonSocial,'A',(-1)*a.iImporteSinIva,'','',@dFechaPago,0,0
	from #tmpInfoPolizas as a
	join maestromuebles.dbo.CenProveedores d with(nolock)
	on a.NumProveedor = d.NumProveedor
	cross join (select distinct Num_Cuenta,Num_SubCuenta,Num_SSubCuenta 
	            from maestromuebles.dbo.CenDeptosDivision (nolock) 
	            where Depto=@iDepto and InicialesDeptoDiv=@sInicialesDeptoDiv) c

	--se obtiene el subtipopromocion y tipopromocion
	declare @iSubTipoPromocion tinyint, @iTipoPromocion tinyint
	
	select top 1 @iSubTipoPromocion=clv_subtipopromocion,
				 @iTipoPromocion  = clv_tipopromocion
	from ComprasMuebles.dbo.ctl_comcobrosproveedor with(nolock) 
	where num_depto=@iDepto and clv_inicialesdeptodiv=@sInicialesDeptoDiv and num_folio=@iFolio
	
	SET @sTipoPromocion = CAST(@iSubTipoPromocion as varchar)


SET XACT_ABORT ON; --autorollback	
begin transaction	

	insert into maestromuebles.dbo.CenComprasGeneral(Poliza,Fecha,NumProveedor,NacOImp,Pedimento,FechaPedimento,Paridad,Importe,Impuesto,TotalADV,TotalDTA,TotalIVA,Flete,
                  TipoPoliza,NumEmpleado,cancelada,opc_chequeodecompetencia)
	select Poliza,Fecha,NumProveedor,NacOImp,'','1900-01-01',0,0,0,0,0,0,0,tipopoliza,@iEmpleado,0,opc_chequeodecompetencia
	from #tmpcomprasgeneral

	insert into maestromuebles.dbo.cencompraspolizas(Poliza,Cuenta,SubCuenta,Centro,Descripcion,CargoOAbono,Importe,Factura,Letra,FechaFactura,ImporteSinIva,ImporteDelIva)
	select Poliza,Cuenta,SubCuenta,Centro,Descripcion,CargoOAbono,Importe,Factura,Letra,FechaFactura,ImporteSinIva,ImporteDelIva
	from #tmpcompraspolizas

	insert into maestromuebles.dbo.CenComprasPagos( Poliza,FechaVencimiento,Importe,Concepto,Factura,des_conceptofiscal, des_factura )
    select a.NumPolizaNueva,b.FechaFactura,b.Importe,
	case when 
	@iSubTipoPromocion = 100 
	then 
			SUBSTRING(isnull(@sDescConcepto,'')+' '+isnull(@sFactura,'')+isnull(@sLetra,'') +' '+isnull(@sFechaPromocion,''),1,150)			
	else 												
			SUBSTRING(isnull(@sDescConcepto,'')+' '+isnull(@sFactura,'')+isnull(@sLetra,'') +' '+isnull(@sFechaTipoPromocion,''),1,150)
	end,
	--SUBSTRING(isnull(@sDescConcepto,'')+' '+isnull(@sFactura,'')+isnull(@sLetra,'') +' '+isnull(@sFechaTipoPromocion,''),1,150),
    isnull(@sFactura,'')+ isnull(@sLetra,''),@sDescConcepto, @sFechaTipoPromocion
    from #tmpInfoPolizas as a
    join #tmpPagosBonifSinSku as b
    on a.NumPoliza = b.NumPoliza and a.NumProveedor = b.NumProveedor
    
    insert into maestromuebles.dbo.cencomprasfacpagadas(NumProveedor,Factura,ImporteSinIva,Poliza,FechaPoliza)
    select NumProveedor,isnull(@sFactura,'')+ isnull(@sLetra,''),iImporteSinIva,NumPolizaNueva,GETDATE()
    from #tmpInfoPolizas

	INSERT INTO MaestroMuebles.dbo.CenComprasCodigos(Poliza,Codigo,Cantidad,Costo,CostoTotal,PrecioVta,Pedido,
		UnidadesDeBonif,CostoSalidaBonif, CostoEntradaBonif,Impuesto,ADV,DTA,Factura,importepesos,TipoBonificacion, 
		des_articulo, des_marca, des_modelo)
    SELECT c.NumPolizaNueva,a.Codigo, 0, a.ImporteNeto, 0, 0, 0, a.Cantidad, a.CostoSalida, a.CostoEntrada, 0, 0, 0, 
		@sFactura, 0, 0, b.Articulo, b.Marca, MaestroMuebles.dbo.Fun_Modelo(b.Codigo) 
    FROM #tmpCodigosPolizaPromo a
	INNER JOIN maestromuebles.dbo.cenMaestroCodigo b with(NOLOCK) 
	ON ( a.Codigo = b.Codigo )
	join #tmpInfoPolizas as c
	on a.NumPoliza = c.NumPoliza
    
    --se marca para que ya no se procese
    declare @sClaveUnidad varchar(15)
    if @iSubTipoPromocion<>100
    begin	 
		update ComprasMuebles.dbo.ctl_comcobrosproveedordetalle
		set clv_nomostrarfolio=1,num_poliza=c.NumPolizaNueva
		from ComprasMuebles.dbo.ctl_comcobrosproveedordetalle a(nolock) join ComprasMuebles.dbo.ctl_comcobrosproveedor b(nolock)
		on(a.clv_inicialesdeptodiv=b.clv_inicialesdeptodiv and a.num_folio=b.num_folio and a.codigo=b.codigo)
		join #tmpInfoPolizas as c
		on a.num_proveedor = c.NumProveedor
		join #tmpCodigosPolizaPromo as d
		on a.Codigo = d.Codigo 
		join #tmpPagosBonifSinSku as e
		on a.num_folio = e.Folio and a.num_subfolio = e.SubFolio and a.num_subsubfolio = e.SubSubFolio
		where b.num_depto=@iDepto and a.clv_inicialesdeptodiv=@sInicialesDeptoDiv and a.clv_formacobro=@iFormaCobro
		  
		--SET @sSql='select top 1 @@sClaveUnidad = clv_undiad from ComprasMuebles.dbo.Cat_comdesconceptos(NOLOCK)
		--		  WHERE clv_tipopromocion = '+@iTipoPromocion+' and clv_subtipopromocion = '+@iSubTipoPromocion+''
	    --EXEC sp_executesql @sSql , N'@@sClaveUnidad varchar(5) output',  @sClaveUnidad output

		SELECT top 1 @sClaveUnidad = clv_unidad FROM ComprasMuebles.dbo.Cat_comdesconceptos(NOLOCK) 
		where clv_tipopromocion =+@iTipoPromocion and clv_subtipopromocion = @iSubTipoPromocion
		
    end
    else
    begin 
		update ComprasMuebles.dbo.ctl_comcobrosproveedor
		set clv_nomostrarfolio=1,num_poliza= a.NumPolizaNueva
		from #tmpInfoPolizas as a
		where num_depto=@iDepto and clv_inicialesdeptodiv=@sInicialesDeptoDiv and num_folio=@iFolio 
		  and clv_subtipopromocion=100
 		
		SELECT @sClaveUnidad =  Valor2 FROM MaestroMuebles.dbo.CenConfiguraciones(nolock) where id = 387
    end
    
    -- Executar el sp que solicita la factura al despacho
    IF object_id(''+@sTmpSolicitarFactura+'', 'U')  IS NOT NULL
    BEGIN
	   SET @sSql='select @iClv_tipoFactura = clv_tipofactura from  '+@sTmpSolicitarFactura+''
	   EXEC sp_executesql @sSql , N'@iClv_tipoFactura int output',  @iClv_tipoFactura output

		IF @iClv_tipoFactura = 2
		BEGIN
			UPDATE maestromuebles.dbo.cencompraspolizas SET clv_estatus = 8 , clv_tipofactura = 2 WHERE Poliza = @iFolioPoliza
    	END 
    	ELSE
    	BEGIN
			IF @iSubTipoPromocion = 100 
			BEGIN
				SET @sConceptoDespacho = SUBSTRING(isnull(@sDescConcepto,'')+' '+isnull(@sFactura,'')+isnull(@sLetra,'') +' '+isnull(@sFechaPromocion,''),1,150)
			END
			ELSE				
			BEGIN
				SET @sConceptoDespacho = SUBSTRING(isnull(@sDescConcepto,'')+' '+isnull(@sFactura,'')+isnull(@sLetra,'') +' '+isnull(@sFechaTipoPromocion,''),1,150)
			END			
    		EXEC MaestroMuebles.dbo.proc_polsolicitarfacturafiscal  3, @iProveedor, @iFolioPoliza, @iDepto, @sInicialesDeptoDiv, @iFolio, @iSubFolio, @iSubSubFolio,@iEmpleado,@sConceptoDespacho, @sClaveUnidad,@iIva,@sTipoPromocion
    	END	
    	
    	SET @sSql ='drop table '+@sTmpSolicitarFactura+''
    	EXECUTE (@sSql)
    END
    ELSE
    BEGIN
		UPDATE maestromuebles.dbo.cencompraspolizas SET clv_estatus = 8 , clv_tipofactura = 2 WHERE Poliza = @iFolioPoliza
    END
    	
commit transaction

	select NumPolizaNueva as poliza from #tmpInfoPolizas

end
GO
GRANT EXECUTE ON proc_grabarPolizaPromociones TO sysprogsmuebles
GO
PROC_DOCUMENTACION 'proc_grabarPolizaPromociones','','genera la poliza de promociones'
GO  
PROC_DOCUMENTACION 'proc_grabarPolizaPromociones','@iDepto','departamento de la poliza'
GO  
PROC_DOCUMENTACION 'proc_grabarPolizaPromociones','@sInicialesDeptoDiv','inicial del departamento'
GO  
PROC_DOCUMENTACION 'proc_grabarPolizaPromociones','@iFolio','folio al que se le genera poliza'
GO  
PROC_DOCUMENTACION 'proc_grabarPolizaPromociones','@iSubFolio','subfolio al que se le genera poliza'
GO  
PROC_DOCUMENTACION 'proc_grabarPolizaPromociones','@iSubSubFolio','subsubfolio al que se le genera poliza'
GO  
PROC_DOCUMENTACION 'proc_grabarPolizaPromociones','@iProveedor','proveedor de la poliza'
GO  
PROC_DOCUMENTACION 'proc_grabarPolizaPromociones','@iIva','iva de la poliza'
GO  
PROC_DOCUMENTACION 'proc_grabarPolizaPromociones','@iImporteSinIva','importe sin iva de la poliza'
GO  
PROC_DOCUMENTACION 'proc_grabarPolizaPromociones','@iImporteDelIva','importe del iva de la poliza'
GO  
PROC_DOCUMENTACION 'proc_grabarPolizaPromociones','@iEmpleado','empleado que genera la poliza'
GO  
PROC_DOCUMENTACION 'proc_grabarPolizaPromociones','@cIdentificador','identificador unico para las tablas temporales'
GO  

