# Validador-de-Bandeiras
Desafio DIO para validação de bandeiras de cartão de crédito
Este projeto implementa um identificador de bandeiras de cartões de crédito, com validação completa usando expressões regulares e o algoritmo de **Luhn**, garantindo que o número do cartão seja válido antes da identificação da bandeira.
import re
}
class BandeiraCartao:
    bandeiras = {
        "Visa": r"^4\d{12}(\d{3})?$",
        "Mastercard": r"^5[1-5]\d{14}$|^2(2[2-9][1-9]|[3-6]\d{2}|7([01]\d|20))\d{12}$",
        "Amex": r"^3[47]\d{13}$",
        "Diners Club": r"^3(0[0-5]|[68]\d)\d{11}$",
        "Discover": r"^6(011|5\d{2})\d{12}$",
        "JCB": r"^35(2[89]|[3-8]\d)\d{12}$",
        "Voyager": r"^8699\d{11}$",
        "Hipercard": r"^(606282\d{10}(\d{3})?|3841\d{15})$",
        "Aura": r"^50[0-9]{14,17}$"
    }

    @classmethod
    def identificar(cls, numero: str) -> str:
        numero = re.sub(r'\D', '', numero)
        if not numero:
            raise CartaoInvalidoException("Número do cartão vazio ou inválido.")

        if not cls.validar_luhn(numero):
            raise CartaoInvalidoException("Número do cartão inválido pelo algoritmo de Luhn.")

        for bandeira, padrao in cls.bandeiras.items():
            if re.fullmatch(padrao, numero):
                return bandeira

        raise CartaoInvalidoException("Bandeira não reconhecida para o número informado.")

    @staticmethod
    def validar_luhn(numero: str) -> bool:
        total = 0
        reverso = numero[::-1]
        for i, digito in enumerate(reverso):
            n = int(digito)
            if i % 2 == 1:
                n *= 2
                if n > 9:
                    n -= 9
            total += n
        return total % 10 == 0
