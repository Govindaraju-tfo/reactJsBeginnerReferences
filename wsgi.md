#wsgi.py

from src.application import app

if __name__ == '__main__':
    app.run(debug=False)
    
    
    
  # config.json
  
  {
    "agent": {
        "run_as_user": "root"
    },
    "logs": {
        "force_flush_interval": 5,
        "logs_collected": {
            "files": {
                "collect_list": [
                    {
                        "file_path": "/tmp/application.log",
                        "log_group_name": "/var/log/apistore/apip/addon",
                        "log_stream_name": "{instance_id}  -  {hostname}",
                        "timestamp_format": "%Y-%m-%d %H:%M:%S"
                    }
                ]
            }
        }
    }
}


# preValidateRequestUserDetails.py
import textwrap
import os
from flask import Flask, jsonify, request
from flask_restful import Resource, Api, reqparse
from flask import abort
import logging
import json
from functools import wraps

import requests
from requests.exceptions import HTTPError,ConnectionError
from werkzeug.exceptions import BadRequest,Unauthorized

ABC_SESSION_URL = os.environ.get('ABC_SESSION_URL')
ABC_SESSION_URL = os.environ.get('ABC_SESSION_URL')


def require_login(api_method):
    @wraps(api_method)

    def check_api_token(*args, **kwargs):
        token = None
        client_id = None
        exception_error_msg = "Not Authorized, Error while validating login session."

        if "X-CLIENT-ID" in request.headers:
            client_id = request.headers["X-CLIENT-ID"]

        if "Authorization" in request.headers:
            token = request.headers["Authorization"]

        http_args = request.args
        source = http_args.get('source')

        try:
            logging.info("client_id : %s " % (client_id))
            logging.info("token : %s " % (token))

            if (client_id is None ) and ( source is not None or source!=''):
                
                #validate session
                if token:
                    logging.info("Auth in request : %s " % (token))
                    print("auth token : ", token)
                    if token.startswith("Bearer"):
                        token = token[len("Bearer") + 1:]
                    
                    logging.info("source : %s " % (source))
                    final_session_url = ''
                    if (source == 'aep' or source == 'cmp'):
                        final_session_url=ABC_SESSION_URL
                    else:
                        final_session_url=ABC_SESSION_URL
                    
                    session_data = {}
                    session_data['token'] = token
                    headers = {'Content-type': 'application/json', 'Accept': 'application/json'}
                    session_valid_response = requests.post(final_session_url,json=session_data,headers=headers)
                    response_status_code = session_valid_response.status_code

                    logging.info("session validation status code : %s " % (response_status_code))
                    logging.info("session validation response : %s " % (session_valid_response))

                    print("session_valid_response.status_code",response_status_code)
                    print("session_valid_response=",session_valid_response)
                    
                    if response_status_code == 200:
                        res_json = session_valid_response.json()
                        
                        if "user" not in res_json:
                            msg = "Not Authorized, Your login token expired."
                            logging.error(msg)
                            abort(401,msg)
                        
                        else:
                            return api_method(*args, **kwargs)

                    else:    
                        msg = "Not authorized, Failed to validate user session token."
                        logging.error(msg)
                        abort(401,msg)

                else:
                    msg = "Not authorized, Authorization token is required."
                    logging.error(msg)
                    abort(401,msg)

        except BadRequest:
            raise
        except Unauthorized:
            raise
        except ConnectionError as conn_error:
            print(f'ConnectionError: {conn_error}')
            logging.error(f'ConnectionError: {conn_error}')
            logging.error(exception_error_msg)
            abort(401,exception_error_msg)
        except HTTPError as http_err:
            logging.error(f'HTTPError: {http_err}')
            logging.error(exception_error_msg)
            abort(401,exception_error_msg)
        except Exception as err:
            logging.error(f"Unexpected {err}, {type(err)}")
            logging.error(exception_error_msg)
            abort(401,exception_error_msg)

        #forward request
        return api_method(*args, **kwargs) 

    return check_api_token
    
    
    # dbmanager.py
    
    from uuid import uuid4

from flask_sqlalchemy import SQLAlchemy
from sqlalchemy.dialects.postgresql import UUID

db = SQLAlchemy()


class CModel(db.Model):
    __tablename__ = 'aaass_mock'
    __table_args__ = {"schema": "box"}

    id = db.Column(UUID(as_uuid=True), default=uuid4, primary_key=True)
    appid = db.Column(db.String())
    groupid = db.Column(db.String())
    thumbprint = db.Column(db.String())
    serialno = db.Column(db.String())
    cn = db.Column(db.String())

    def __init__(self, appid, groupid, thumbprint, serialno,cn):
        self.appid = appid
        self.groupid = groupid
        self.thumbprint = thumbprint
        self.serialno = serialno
        self.cn = cn

    def __repr__(self):
        return f"<CModel {self.appid}>"
        
   # certificatemanager.py
   import binascii
import os
import sys
from datetime import datetime, timedelta

from cryptography import x509
from cryptography.hazmat.backends import default_backend
from cryptography.hazmat.primitives import hashes, serialization
from cryptography.hazmat.primitives.serialization import load_pem_private_key
from cryptography.x509.base import load_pem_x509_certificate, load_pem_x509_csr
from cryptography.x509.oid import NameOID
from src import dbmanager
from flask import abort
import logging

CA_CERT_SECRET = os.environ.get('CA_CERT_SECRET')
CERT_VALIDITY_PERIOD = int(os.environ.get('CERT_VALIDITY_PERIOD'))

class certmanager:

    def load_pem(self,
                 entity_type=None, path=None, pem_text=None, password=None
                 ):
        """ Load pem encoded entity

        Attributes:
            entity_type (str): certificate|key|csr.
            path (str): Absolute path to file containing pem encoded entity.
            pem_text (str): String object with pem encoded entity
            password (str): Password to use in case entity is protected
        """
        if path and pem_text:
            raise ValueError("Path and pem_text are mutually exclusive")

        if path:
            with open(path, "rb") as f:
                pem_text = f.read()

        if entity_type == "certificate":
            return load_pem_x509_certificate(
                pem_text, backend=default_backend()
            )
        elif entity_type == "csr":
            return load_pem_x509_csr(pem_text, backend=default_backend())
        elif entity_type == "key":
            return load_pem_private_key(
                pem_text, password=password, backend=default_backend(),
            )
        else:
            raise ValueError("Unsupported entity_type {}".format(entity_type))

    def issue_certificate(self, int_cert, int_pkey, sb_csr, gen_cert):
        cert_obj = {}
        password = CA_CERT_SECRET
        cert_path = os.path.join(sys.path[0], "cert/")
        ca_cert = self.load_pem(entity_type="certificate", path=cert_path + "ca/intermediate/certs/" + int_cert)
        ca_key = self.load_pem(entity_type="key", path=cert_path + "ca/intermediate/private/" + int_pkey,
                               password=password.encode('ascii'))
        # csr = self.load_pem(entity_type="csr", path=cert_path + sb_csr)
        csr = self.load_pem(entity_type="csr", pem_text=sb_csr)
        logging.info(csr.subject)

        #Get CN name from CSR and validate if such name already exists
        cn = csr.subject.get_attributes_for_oid(NameOID.COMMON_NAME)[0].value
        cn = cn.lower()
        logging.info("common name : %s " % (cn))
        dbRow = dbmanager.CModel.query.filter(dbmanager.CModel.cn == cn).first()
      
        #Abort request if Common Name already exists
        if (dbRow is not None):
            msg = "certificate with common name: '{0}' is already registered".format(cn)
            logging.error(msg)
            abort(400,msg)
    

        sbclient_cert = self.load_pem(entity_type="certificate", path=cert_path + "sb-template.cert.pem")
        serial_number = x509.random_serial_number()

        builder = x509.CertificateBuilder(
            issuer_name=ca_cert.subject,
            subject_name=csr.subject,
            public_key=csr.public_key(),
            not_valid_before=datetime.utcnow(),
            not_valid_after=datetime.utcnow() + timedelta(days=CERT_VALIDITY_PERIOD),
            extensions=sbclient_cert.extensions,
            serial_number=serial_number
        )

        certificate = builder.sign(
            private_key=ca_key,
            algorithm=hashes.SHA256(),
            backend=default_backend(),
        )

        with open(os.path.join(sys.path[0], "cert/" + gen_cert), 'wb') as f:
            f.write(certificate.public_bytes(
                encoding=serialization.Encoding.PEM
            ))

        logging.info('===============================================')
        sbclient2_cert = self.load_pem(entity_type="certificate", path=cert_path + gen_cert)
        logging.info(sbclient2_cert.extensions[0])
        logging.info(sbclient2_cert.signature_algorithm_oid)
        logging.info(sbclient2_cert.serial_number)
        logging.info(sbclient2_cert.signature_hash_algorithm)
        logging.info(sbclient2_cert.public_key())
        logging.info(sbclient2_cert.subject)
        logging.info(sbclient2_cert.issuer)
        logging.info('===============================================')

        sha1_fingerprint = certificate.fingerprint(hashes.SHA1())
        sha1_fingerprint = binascii.hexlify(sha1_fingerprint)

        cert_obj = {
            "certificate": certificate.public_bytes(
                encoding=serialization.Encoding.PEM
            ),
            "serial_number": serial_number,
            "thumbPrint": sha1_fingerprint,
            "validFrom": certificate.not_valid_before,
            "validTo": certificate.not_valid_after,
            "cn" : cn
        }

        return cert_obj


# applicaiton.py

import textwrap
import os
from flask import Flask, jsonify, request
from flask_restful import Resource, Api, reqparse
import logging
import json
from src import certificatemanager
from src import dbmanager
from src.preValidateRequestUserDetails import require_login

parser = reqparse.RequestParser()
app = Flask(__name__)

CA_CERT_SECRET = os.environ.get('CA_CERT_SECRET')
LOG_FILE_LOC = os.environ.get('LOG_FILE_LOC')

logging.basicConfig(filename=LOG_FILE_LOC, level=logging.DEBUG, format=f'%(asctime)s %(levelname)s %(name)s %(threadName)s : %(message)s')

DB_AUTH_USER = os.environ.get('DB_AUTH_USER')
DB_AUTH_PASSWORD = os.environ.get('DB_AUTH_PASSWORD')
DB_AUTH_HOST = os.environ.get('DB_AUTH_HOST')
DB_AUTH_PORT = os.environ.get('DB_AUTH_PORT')
DB_AUTH_DATABASE = os.environ.get('DB_AUTH_DATABASE')
SQLALCHEMY_DATABASE_URI="postgresql://"+DB_AUTH_USER+":"+DB_AUTH_PASSWORD+"@"+DB_AUTH_HOST+":"+DB_AUTH_PORT+"/"+DB_AUTH_DATABASE
app.config['SQLALCHEMY_DATABASE_URI'] =  SQLALCHEMY_DATABASE_URI

dbmanager.db.init_app(app)
api = Api(app)

@app.errorhandler(Exception)
def handle_unknown_error(exception):
    """
    Registering an error hander for unknown error such as
    - database connection error
    - SQL exception error
    """
    logging.error('handle_unknown_error type is= %s', exception.__class__.__name__)
    logging.error('Error at %s', 'handle_unknown_error-2', exc_info=exception)
    response = jsonify("{'message': 'Unknown Error','code':500}")
    response.status_code = 500
    return response

class signCsr(Resource):
    method_decorators =  [require_login]
    def post(self,*args,**kwargs):
        app.logger.info('Inside SignCsr')
        certm = certificatemanager.certmanager()
        parser.add_argument('request', type=dict)
        http_args = parser.parse_args()
        request_body = request.json
        csrraw = request_body['request']['certInfo']['csr']

        csr_head = "-----BEGIN CERTIFICATE REQUEST-----\n"
        csr_tail = "\n-----END CERTIFICATE REQUEST-----\n"
        sb_csr = '\n'.join(textwrap.wrap(csrraw, 64))
        sb_csr = csr_head + sb_csr + csr_tail
        sb_csr = sb_csr.encode("utf-8")

        result = certm.issue_certificate("intermediate.cert.pem",
                                         "intermediate.key.pem",
                                         sb_csr,
                                         "sandbox-client4.cert.pem")

        new_model = dbmanager.CModel(appid=request_body['request']['user']['appId'],
                                     groupid=request_body['request']['user']['groupId'],
                                     thumbprint=str(result["thumbPrint"], encoding='utf-8'),
                                     serialno='{0:x}'.format(result["serial_number"]),
                                     cn=str(result["cn"]))

        dbmanager.db.session.add(new_model)
        dbmanager.db.session.commit()

        responseobj = {
            "signedCrt": str(result["certificate"], encoding='utf-8'),
            "thumbPrint": str(result["thumbPrint"], encoding='utf-8'),
            'serialNumber': '{0:x}'.format(result["serial_number"]),
            "status": "ACTIVE",
            "validFrom": result["validFrom"],
            "validTo": result["validTo"]
        }

        return jsonify(statuscode="100",
                       errorMessage="Certificate signing - Success",
                       certificateResponse=responseobj)


class validateCert(Resource):
    def post(self):
        parser.add_argument('request', type=dict)
        args = parser.parse_args()
        serialno = args['request']['serialno']
        dbRow = dbmanager.CModel.query.filter(dbmanager.CModel.serialno == serialno).first()
        result = {
            "appid": dbRow.appid,
            "clientId": dbRow.groupid,
            "thumbprint": dbRow.thumbprint,
        }
        return {'response': result}


class HealthCheck(Resource):
    def get(self):
        return {'status': 'ok'}

# Return all header dynamically, this will be useful debug akamai calls
class testMTLS(Resource):
    def get(self):
        response = app.response_class(
                    response=json.dumps(dict(request.headers), indent = 4),
                    status=200,
                    mimetype='application/json'
                    )
        return response

class DBCheck(Resource):
    def get(self):
        models = dbmanager.CModel.query.all()
        results = [
            {
                "appid": model.appid,
            } for model in models
        ]
        return {
            'results': results
        }


class DBInsertCheck(Resource):
    def post(self):
        parser.add_argument('request', type=dict)
        args = parser.parse_args()
        new_model = dbmanager.CModel(appid=args['request']['appid'], groupid=args['request']['groupid'],
                                     thumbprint=args['request']['thumbprint'], serialno=args['request']['serialno'])
        dbmanager.db.session.add(new_model)
        dbmanager.db.session.commit()
        return {'status': args['request']['appid']}


api.add_resource(signCsr, '/uaas/services/v1/certificate/signCsr')
api.add_resource(validateCert, '/uaas/services/v1/certificate/validateCert')
api.add_resource(HealthCheck, '/uaas/health')
api.add_resource(testMTLS, '/uaas/testMTLS')
api.add_resource(DBCheck, '/uaas/DBCheck')
api.add_resource(DBInsertCheck, '/uaas/DBInsertCheck')
