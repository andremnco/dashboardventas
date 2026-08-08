/*******************************************************
 * VIDA SANA 360 ERP
 * BACKEND - GOOGLE APPS SCRIPT
 *
 * PESTAÑAS UTILIZADAS:
 * 1. Productos
 * 2. Clientes
 * 3. Cotizaciones
 * 4. Ventas_Historial
 *******************************************************/

const SHEET_ID = '1tC_unwVitc1YI5aC_v9RaFxEJMFLDR3JYWLpu-4nTxQ';


/* =====================================================
   1. ABRIR GOOGLE SHEETS
   ===================================================== */

function getSpreadsheet_() {
  return SpreadsheetApp.openById(SHEET_ID);
}


/* =====================================================
   2. MOSTRAR INDEX.HTML
   ===================================================== */

function doGet() {
  return HtmlService
    .createHtmlOutputFromFile('Index')
    .setTitle('Vida Sana 360 ERP')
    .setXFrameOptionsMode(HtmlService.XFrameOptionsMode.ALLOWALL);
}


/* =====================================================
   3. LEER UNA PESTAÑA COMPLETA
   ===================================================== */

function readSheet_(sheetName) {

  const ss = getSpreadsheet_();
  const sheet = ss.getSheetByName(sheetName);

  if (!sheet) {
    throw new Error(
      'No existe la pestaña "' + sheetName + '" en Google Sheets.'
    );
  }

  const lastRow = sheet.getLastRow();
  const lastColumn = sheet.getLastColumn();

  if (lastRow < 1 || lastColumn < 1) {
    return [];
  }

  const values = sheet
    .getRange(1, 1, lastRow, lastColumn)
    .getValues();

  if (values.length < 2) {
    return [];
  }

  const headers = values[0].map(function(header) {
    return String(header || '').trim();
  });

  const result = [];

  for (let i = 1; i < values.length; i++) {

    const row = values[i];

    // Ignorar filas completamente vacías
    const empty = row.every(function(value) {
      return value === '' || value === null;
    });

    if (empty) continue;

    const obj = {};

    headers.forEach(function(header, index) {

      if (!header) return;

      let value = row[index];

      // Convertir fechas a texto para que Apps Script
      // pueda enviarlas correctamente al HTML.
      if (value instanceof Date) {
        value = Utilities.formatDate(
          value,
          Session.getScriptTimeZone(),
          'yyyy-MM-dd'
        );
      }

      obj[header] = value;
    });

    result.push(obj);
  }

  return result;
}


/* =====================================================
   4. FUNCIONES DE CONVERSIÓN
   ===================================================== */

function number_(value) {

  if (value === null || value === undefined || value === '') {
    return 0;
  }

  if (typeof value === 'number') {
    return value;
  }

  // Permitir números escritos como:
  // 1234
  // 1234.50
  // 1,234.50
  // 1234,50

  let text = String(value).trim();

  text = text.replace(/[^\d,.-]/g, '');

  // Si tiene coma y punto:
  // 1,234.50 -> 1234.50
  if (text.indexOf(',') !== -1 && text.indexOf('.') !== -1) {
    text = text.replace(/,/g, '');
  }

  // Si solamente tiene coma:
  // 1234,50 -> 1234.50
  else if (text.indexOf(',') !== -1) {
    text = text.replace(',', '.');
  }

  const result = Number(text);

  return isNaN(result) ? 0 : result;
}


function text_(value) {

  if (value === null || value === undefined) {
    return '';
  }

  return String(value).trim();
}


/* =====================================================
   5. PRODUCTOS
   ===================================================== */

function getProducts_() {

  const rows = readSheet_('Productos');

  return rows.map(function(row) {

    return {
      id: text_(row.id),

      name: text_(row.name),

      category: text_(row.category),

      lab: text_(row.lab),

      price: number_(row.price),

      cost: number_(row.cost),

      stock: number_(row.stock),

      minStock: number_(row.minStock),

      sales30: number_(row.sales30)
    };

  }).filter(function(product) {

    // Una fila de producto debe tener al menos ID o nombre.
    return product.id !== '' || product.name !== '';

  });
}


/* =====================================================
   6. CLIENTES
   ===================================================== */

function getClients_() {

  const rows = readSheet_('Clientes');

  return rows.map(function(row) {

    return {

      id: text_(row.id),

      name: text_(row.name),

      phone: text_(row.phone),

      email: text_(row.email),

      condition: text_(row.condition),

      orders: number_(row.orders),

      totalSpent: number_(row.totalSpent),

      lastOrder: text_(row.lastOrder),

      segment: text_(row.segment),

      color: text_(row.color)

    };

  }).filter(function(client) {

    return client.id !== '' || client.name !== '';

  });
}


/* =====================================================
   7. COTIZACIONES
   ===================================================== */

function getQuotes_() {

  const rows = readSheet_('Cotizaciones');

  return rows.map(function(row) {

    return {

      id: text_(row.id),

      client: text_(row.client),

      total: number_(row.total),

      status: text_(row.status),

      date: text_(row.date),

      validUntil: text_(row.validUntil)

    };

  }).filter(function(quote) {

    return quote.id !== '' || quote.client !== '';

  });
}


/* =====================================================
   8. HISTORIAL DE VENTAS
   ===================================================== */

function getSalesHistory_() {

  const rows = readSheet_('Ventas_Historial');

  return rows.map(function(row) {

    return {

      order: text_(row.order),

      client: text_(row.client),

      date: text_(row.date),

      channel: text_(row.channel),

      payment: text_(row.payment),

      total: number_(row.total),

      status: text_(row.status)

    };

  }).filter(function(sale) {

    return sale.order !== '' || sale.client !== '';

  });
}


/* =====================================================
   9. CATEGORÍAS REALES
   ===================================================== */

function getCategories_(products) {

  const categories = [];

  products.forEach(function(product) {

    const category = text_(product.category);

    if (
      category !== '' &&
      categories.indexOf(category) === -1
    ) {
      categories.push(category);
    }

  });

  return categories;
}


/* =====================================================
   10. ICONOS DE CATEGORÍAS
   ===================================================== */

function getCategoryIcons_() {

  return {

    'Vitaminas': {
      icon: 'ph-sun',
      color: '#F59E0B'
    },

    'Minerales': {
      icon: 'ph-mountains',
      color: '#106FEB'
    },

    'Proteínas': {
      icon: 'ph-barbell',
      color: '#EF4444'
    },

    'Colágeno': {
      icon: 'ph-drop',
      color: '#EC4899'
    },

    'Omega y Ácidos Grasos': {
      icon: 'ph-fish',
      color: '#0EA5E9'
    },

    'Probióticos': {
      icon: 'ph-bacteria',
      color: '#10B981'
    },

    'Adaptógenos': {
      icon: 'ph-leaf',
      color: '#8B5CF6'
    }

  };

}


/* =====================================================
   11. CALCULAR DATOS DEL DASHBOARD
   ===================================================== */

function calculateDashboard_(products, clients, quotes, sales) {

  let revenue = 0;
  let orders = 0;

  sales.forEach(function(sale) {

    revenue += number_(sale.total);

    orders++;

  });


  let inventoryValue = 0;
  let lowStock = 0;

  products.forEach(function(product) {

    inventoryValue +=
      number_(product.cost) *
      number_(product.stock);

    if (
      number_(product.stock) <=
      number_(product.minStock)
    ) {
      lowStock++;
    }

  });


  let clientsWithOrders = 0;

  clients.forEach(function(client) {

    if (number_(client.orders) > 0) {
      clientsWithOrders++;
    }

  });


  let averageTicket = 0;

  if (orders > 0) {
    averageTicket = revenue / orders;
  }


  /*
   * Ventas por día.
   *
   * Se generan solamente a partir de las ventas reales
   * de Ventas_Historial.
   */

  const revenueByDay = {};

  sales.forEach(function(sale) {

    const date = text_(sale.date);

    if (!date) return;

    if (!revenueByDay[date]) {
      revenueByDay[date] = 0;
    }

    revenueByDay[date] += number_(sale.total);

  });


  const sortedDates = Object.keys(revenueByDay).sort();

  const revenueSeries = sortedDates.map(function(date) {

    return revenueByDay[date];

  });


  return {

    revenue: revenue,

    orders: orders,

    inventoryValue: inventoryValue,

    lowStock: lowStock,

    clients: clients.length,

    clientsWithOrders: clientsWithOrders,

    averageTicket: averageTicket,

    revenueByDate: revenueByDay,

    revenueSeries: revenueSeries,

    revenueDates: sortedDates

  };

}


/* =====================================================
   12. FUNCIÓN PRINCIPAL PARA INDEX.HTML
   ===================================================== */

function obtenerDatosERP() {

  try {

    const products = getProducts_();

    const clients = getClients_();

    const quotes = getQuotes_();

    const salesHistory = getSalesHistory_();

    const dashboard =
      calculateDashboard_(
        products,
        clients,
        quotes,
        salesHistory
      );


    return {

      // DATOS REALES
      products: products,

      clients: clients,

      quotes: quotes,

      salesHistory: salesHistory,


      // DATOS CALCULADOS DESDE LAS HOJAS
      categories: getCategories_(products),

      catIcons: getCategoryIcons_(),

      dashboard: dashboard,


      // Datos vacíos porque actualmente
      // no existen pestañas para estas entidades.
      providers: [],

      couriers: [],

      agencies: [],

      platforms: [],

      channels: [],

      sellers: [],

      districts: [],

      returns: [],

      activity: [],


      // No inventamos estadísticas.
      revenueByDay: dashboard.revenueSeries,

      salesTrend30: [],

      goal: {
        target: 0,
        current: dashboard.revenue
      },


      posCart: []

    };

  } catch (error) {

    console.error(
      'Error en obtenerDatosERP:',
      error
    );

    throw new Error(
      'No se pudieron cargar los datos del ERP: ' +
      error.message
    );

  }

}


/* =====================================================
   13. CREAR CLIENTE
   ===================================================== */

function crearCliente(clientData) {

  if (!clientData) {
    throw new Error('No se recibieron datos del cliente.');
  }

  const ss = getSpreadsheet_();

  const sheet = ss.getSheetByName('Clientes');

  if (!sheet) {
    throw new Error(
      'No existe la pestaña "Clientes".'
    );
  }


  const id =
    text_(clientData.id) ||
    ('CLI-' + new Date().getTime());


  sheet.appendRow([

    id,

    text_(clientData.name),

    text_(clientData.phone),

    text_(clientData.email),

    text_(clientData.condition),

    number_(clientData.orders),

    number_(clientData.totalSpent),

    text_(clientData.lastOrder),

    text_(clientData.segment),

    text_(clientData.color)

  ]);


  return true;

}


/* =====================================================
   14. COMPATIBILIDAD CON saveClient()
   ===================================================== */

function saveClient(clientData) {

  return crearCliente(clientData);

}


/* =====================================================
   15. CREAR PRODUCTO
   ===================================================== */

function saveProduct(productData) {

  if (!productData) {
    throw new Error(
      'No se recibieron datos del producto.'
    );
  }

  const ss = getSpreadsheet_();

  const sheet = ss.getSheetByName('Productos');

  if (!sheet) {
    throw new Error(
      'No existe la pestaña "Productos".'
    );
  }


  const id =
    text_(productData.id) ||
    ('PROD-' + new Date().getTime());


  sheet.appendRow([

    id,

    text_(productData.name),

    text_(productData.category),

    text_(productData.lab),

    number_(productData.price),

    number_(productData.cost),

    number_(productData.stock),

    number_(productData.minStock),

    number_(productData.sales30)

  ]);


  return true;

}


/* =====================================================
   16. CREAR COTIZACIÓN
   ===================================================== */

function saveQuote(quoteData) {

  if (!quoteData) {
    throw new Error(
      'No se recibieron datos de la cotización.'
    );
  }

  const ss = getSpreadsheet_();

  const sheet =
    ss.getSheetByName('Cotizaciones');

  if (!sheet) {
    throw new Error(
      'No existe la pestaña "Cotizaciones".'
    );
  }


  const id =
    text_(quoteData.id) ||
    ('COT-' + new Date().getTime());


  sheet.appendRow([

    id,

    text_(quoteData.client),

    number_(quoteData.total),

    text_(quoteData.status) || 'Por enviar',

    text_(quoteData.date),

    text_(quoteData.validUntil)

  ]);


  return true;

}


/* =====================================================
   17. PROCESAR VENTA
   ===================================================== */

function processSaleBackend(
  saleData,
  cartItems
) {

  if (!saleData) {
    throw new Error(
      'No se recibieron los datos de la venta.'
    );
  }


  const ss = getSpreadsheet_();

  const salesSheet =
    ss.getSheetByName('Ventas_Historial');

  const productsSheet =
    ss.getSheetByName('Productos');


  if (!salesSheet) {
    throw new Error(
      'No existe la pestaña "Ventas_Historial".'
    );
  }

  if (!productsSheet) {
    throw new Error(
      'No existe la pestaña "Productos".'
    );
  }


  /*
   * 1. GUARDAR LA VENTA
   */

  const order =
    text_(saleData.order) ||
    ('VTA-' + new Date().getTime());


  salesSheet.appendRow([

    order,

    text_(saleData.clientName),

    text_(saleData.date),

    text_(saleData.channel),

    text_(saleData.payment),

    number_(saleData.total),

    text_(saleData.status) || 'Completado'

  ]);


  /*
   * 2. DESCONTAR STOCK
   */

  if (
    Array.isArray(cartItems) &&
    cartItems.length > 0
  ) {

    const lastRow =
      productsSheet.getLastRow();

    const lastColumn =
      productsSheet.getLastColumn();


    if (
      lastRow >= 2 &&
      lastColumn >= 9
    ) {

      const data =
        productsSheet
          .getRange(
            2,
            1,
            lastRow - 1,
            9
          )
          .getValues();


      cartItems.forEach(function(item) {

        const itemId =
          text_(item.id);

        const quantity =
          number_(item.qty);


        if (!itemId || quantity <= 0) {
          return;
        }


        for (
          let i = 0;
          i < data.length;
          i++
        ) {

          const productId =
            text_(data[i][0]);


          if (productId === itemId) {

            const currentStock =
              number_(data[i][6]);

            const currentSales30 =
              number_(data[i][8]);


            const newStock =
              Math.max(
                0,
                currentStock - quantity
              );


            const newSales30 =
              currentSales30 + quantity;


            // Columna G = stock
            productsSheet
              .getRange(i + 2, 7)
              .setValue(newStock);


            // Columna I = sales30
            productsSheet
              .getRange(i + 2, 9)
              .setValue(newSales30);


            break;

          }

        }

      });

    }

  }


  return true;

}


/* =====================================================
   18. REPONER PRODUCTO
   ===================================================== */

function reponerProducto(
  productId,
  quantity
) {

  const id = text_(productId);

  const qty = number_(quantity);


  if (!id) {
    throw new Error(
      'No se indicó el ID del producto.'
    );
  }


  if (qty <= 0) {
    throw new Error(
      'La cantidad a reponer debe ser mayor que cero.'
    );
  }


  const ss = getSpreadsheet_();

  const sheet =
    ss.getSheetByName('Productos');


  if (!sheet) {
    throw new Error(
      'No existe la pestaña "Productos".'
    );
  }


  const lastRow =
    sheet.getLastRow();


  if (lastRow < 2) {
    throw new Error(
      'No hay productos registrados.'
    );
  }


  const data =
    sheet
      .getRange(
        2,
        1,
        lastRow - 1,
        9
      )
      .getValues();


  for (
    let i = 0;
    i < data.length;
    i++
  ) {

    if (text_(data[i][0]) === id) {

      const currentStock =
        number_(data[i][6]);


      sheet
        .getRange(i + 2, 7)
        .setValue(
          currentStock + qty
        );


      return true;

    }

  }


  throw new Error(
    'No se encontró el producto con ID: ' + id
  );

}


/* =====================================================
   19. GET INITIAL DATA
   ===================================================== */

/*
 * Compatibilidad con tu HTML anterior.
 *
 * Si alguna parte del index.html todavía llama
 * a getInitialData(), funcionará igualmente.
 */

function getInitialData() {

  return obtenerDatosERP();

}


/* =====================================================
   20. PRUEBA DE CONEXIÓN
   ===================================================== */

function probarConexion() {

  const datos =
    obtenerDatosERP();


  console.log(
    '======================================'
  );

  console.log(
    '   VIDA SANA 360 - CONEXIÓN'
  );

  console.log(
    '======================================'
  );


  console.log(
    'Productos: ' +
    datos.products.length
  );


  console.log(
    'Clientes: ' +
    datos.clients.length
  );


  console.log(
    'Cotizaciones: ' +
    datos.quotes.length
  );


  console.log(
    'Ventas: ' +
    datos.salesHistory.length
  );


  if (datos.products.length > 0) {

    console.log(
      'PRIMER PRODUCTO:'
    );

    console.log(
      JSON.stringify(
        datos.products[0],
        null,
        2
      )
    );

  }


  if (datos.clients.length > 0) {

    console.log(
      'PRIMER CLIENTE:'
    );

    console.log(
      JSON.stringify(
        datos.clients[0],
        null,
        2
      )
    );

  }


  return datos;

}


/* =====================================================
   21. DIAGNÓSTICO DE ENCABEZADOS
   ===================================================== */

function diagnosticarHojas() {

  const nombres = [

    'Productos',

    'Clientes',

    'Cotizaciones',

    'Ventas_Historial'

  ];


  const ss =
    getSpreadsheet_();


  const reporte = {};


  nombres.forEach(function(nombre) {

    const sheet =
      ss.getSheetByName(nombre);


    if (!sheet) {

      reporte[nombre] = {
        existe: false
      };

      return;

    }


    const lastRow =
      sheet.getLastRow();

    const lastColumn =
      sheet.getLastColumn();


    const values =
      lastRow > 0 &&
      lastColumn > 0

        ? sheet
            .getRange(
              1,
              1,
              Math.min(lastRow, 2),
              lastColumn
            )
            .getDisplayValues()

        : [];


    reporte[nombre] = {

      existe: true,

      filas: lastRow,

      columnas: lastColumn,

      encabezados:
        values.length
          ? values[0]
          : [],

      primeraFila:
        values.length > 1
          ? values[1]
          : []

    };

  });


  console.log(
    JSON.stringify(
      reporte,
      null,
      2
    )
  );


  return reporte;

}
