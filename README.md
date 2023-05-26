# nuevo5

1.Crear una clase para la base de datos:
import android.content.Context
import android.database.sqlite.SQLiteDatabase
import android.database.sqlite.SQLiteOpenHelper

class MascotaDatabaseHelper(context: Context) : SQLiteOpenHelper(context, DATABASE_NAME, null, DATABASE_VERSION) {

    companion object {
        private const val DATABASE_NAME = "mascota.db"
        private const val DATABASE_VERSION = 1
        private const val TABLE_NAME = "mascota"
        private const val COLUMN_ID = "id"
        private const val COLUMN_NOMBRE = "nombre"
        private const val COLUMN_RAZA = "raza"
        private const val COLUMN_RATING = "rating"
        // Agrega aquí los demás campos del POJO de "Mascota"
    }

    override fun onCreate(db: SQLiteDatabase) {
        val createTableQuery = "CREATE TABLE $TABLE_NAME (" +
                "$COLUMN_ID INTEGER PRIMARY KEY AUTOINCREMENT," +
                "$COLUMN_NOMBRE TEXT," +
                "$COLUMN_RAZA TEXT," +
                "$COLUMN_RATING INTEGER" +
                // Agrega aquí los demás campos de la tabla
                ")"
        db.execSQL(createTableQuery)
    }

    override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
        db.execSQL("DROP TABLE IF EXISTS $TABLE_NAME")
        onCreate(db)
    }
}

2.Implementar métodos para realizar operaciones en la base de datos:
import android.content.ContentValues
import android.content.Context
import android.database.sqlite.SQLiteDatabase

class MascotaDatabase(context: Context) {

    private val dbHelper: MascotaDatabaseHelper = MascotaDatabaseHelper(context)

    fun insertMascota(mascota: Mascota) {
        val db = dbHelper.writableDatabase
        val values = ContentValues().apply {
            put("nombre", mascota.nombre)
            put("raza", mascota.raza)
            put("rating", mascota.rating)
            // Agrega aquí los demás campos de la tabla
        }
        db.insert("mascota", null, values)
        db.close()
    }

    fun getUltimasMascotas(): List<Mascota> {
        val mascotas = mutableListOf<Mascota>()
        val db = dbHelper.readableDatabase
        val selectQuery = "SELECT * FROM mascota ORDER BY id DESC LIMIT 5"
        val cursor = db.rawQuery(selectQuery, null)
        if (cursor.moveToFirst()) {
            do {
                val id = cursor.getInt(cursor.getColumnIndex("id"))
                val nombre = cursor.getString(cursor.getColumnIndex("nombre"))
                val raza = cursor.getString(cursor.getColumnIndex("raza"))
                val rating = cursor.getInt(cursor.getColumnIndex("rating"))
                // Agrega aquí los demás campos de la tabla
                val mascota = Mascota(id, nombre, raza, rating)
                mascotas.add(mascota)
            } while (cursor.moveToNext())
        }
        cursor.close()
        db.close()
        return mascotas
    }
}

3.Utilizar la clase MascotaDatabase para insertar y obtener las últimas mascotas con rating:

// Insertar una mascota en la base de datos
val mascota = Mascota("Mascota 1", "Raza 1", 3)
val mascotaDatabase = MascotaDatabase(context)
mascotaDatabase.insertMascota(mascota)

// Obtener las últimas mascotas con rating
val ultimasMascotas = mascotaDatabase.getUltimasMascotas()



