from flask import Flask, request, jsonify
from flask_restful import Api, Resource
from pymongo import MongoClient


app = Flask(__name__)
api = Api(app)

# Conexão com o MongoDB
client = MongoClient('mongodb://localhost:27017/')
db = client['Qacademico']
estudantes_collection = db['estudantes'] 
usuarios_collection = db['usuarios'] 

# =====================
# CLASSES RESTFUL (ENDPOINTS)
# =====================


class EstudanteResource(Resource):
    def post(self):
        """Inserir um novo estudante"""
        dados = request.json
        estudante_id = estudantes_collection.insert_one(dados).inserted_id
        return jsonify({"message": "Estudante inserido com sucesso", "id": str(estudante_id)})

    def get(self):
        """Listar todos os estudantes"""
        estudantes = list(estudantes_collection.find())
        for estudante in estudantes:
            estudante['_id'] = str(estudante['_id']) 
        return jsonify(estudantes)

class EstudanteDetalheResource(Resource):
    def get(self, id):
        """Procurar estudante por ID"""
        estudante = estudantes_collection.find_one({"_id": id})
        if not estudante:
            return jsonify({"error": "Estudante não encontrado"})
        estudante['_id'] = str(estudante['_id'])
        return jsonify(estudante)

    def put(self, id):
        """Alterar dados de um estudante"""
        dados = request.json
        resultado = estudantes_collection.update_one({"_id": id}, {"$set": dados})
        if resultado.matched_count == 0:
            return jsonify({"error": "Estudante não encontrado"})
        return jsonify({"message": "Estudante atualizado com sucesso"})

    def delete(self, id):
        """Apagar um estudante"""
        resultado = estudantes_collection.delete_one({"_id": id})
        if resultado.deleted_count == 0:
            return jsonify({"error": "Estudante não encontrado"})
        return jsonify({"message": "Estudante excluído com sucesso"})


class UsuarioResource(Resource):
    def post(self):
        """Inserir um novo usuário"""
        dados = request.json
        usuario_id = usuarios_collection.insert_one(dados).inserted_id
        return jsonify({"message": "Usuário inserido com sucesso", "id": str(usuario_id)})

    def get(self):
        """Listar todos os usuários"""
        usuarios = list(usuarios_collection.find())
        for usuario in usuarios:
            usuario['_id'] = str(usuario['_id']) 
        return jsonify(usuarios)

class UsuarioDetalheResource(Resource):
    def get(self, id):
        """Procurar usuário por ID"""
        usuario = usuarios_collection.find_one({"_id": id})
        if not usuario:
            return jsonify({"error": "Usuário não encontrado"})
        usuario['_id'] = str(usuario['_id'])
        return jsonify(usuario)

    def put(self, id):
        """Alterar dados de um usuário"""
        dados = request.json
        resultado = usuarios_collection.update_one({"_id": id}, {"$set": dados})
        if resultado.matched_count == 0:
            return jsonify({"error": "Usuário não encontrado"})
        return jsonify({"message": "Usuário atualizado com sucesso"})

    def delete(self, id):
        """Apagar um usuário"""
        resultado = usuarios_collection.delete_one({"_id": id})
        if resultado.deleted_count == 0:
            return jsonify({"error": "Usuário não encontrado"})
        return jsonify({"message": "Usuário excluído com sucesso"})

# =====================
# REGISTRO DE ENDPOINTS
# =====================


api.add_resource(EstudanteResource, '/estudantes')
api.add_resource(EstudanteDetalheResource, '/estudantes/<string:id>')


api.add_resource(UsuarioResource, '/usuarios')
api.add_resource(UsuarioDetalheResource, '/usuarios/<string:id>')

# =====================
# EXECUÇÃO DO SERVIDOR
# =====================

if __name__ == '__main__':
    app.run(debug=True)
