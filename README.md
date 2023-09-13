# Laboratorio1Tecnicas
package juegocartas;

import java.util.Random;
import javax.swing.JPanel;

public class Jugador {

    private static final int TOTAL_CARTAS = 10;
    private static final Random RANDOM_GENERATOR = new Random();
    private boolean[] enGrupoNombre = new boolean[TOTAL_CARTAS]; // Para seguimiento de grupos por nombre
    private boolean[] enGrupoEscalera = new boolean[TOTAL_CARTAS]; // Para seguimiento de grupos por escalera
    private Carta[] cartas;

    public void repartir() {
        cartas = new Carta[TOTAL_CARTAS];
        for (int i = 0; i < TOTAL_CARTAS; i++) {
            cartas[i] = new Carta(RANDOM_GENERATOR);
        }
    }

    public void mostrar(JPanel pnl) {
        pnl.removeAll();
        for (int i = 0; i < TOTAL_CARTAS; i++) {
            cartas[i].mostrar(pnl, 5 + i * 40, 5);
        }
        pnl.repaint();
    }

    public String getGrupos() { // Método para obtener los grupos
        StringBuilder mensaje = new StringBuilder("No hay grupos\n");

        int[] contadores = new int[NombreCarta.values().length];
        for (int i = 0; i < TOTAL_CARTAS; i++) {
            contadores[cartas[i].getNombre().ordinal()]++;
        }

        int totalGrupos = 0;
        int valorTotalNoGrupo = 0; // Variable para calcular el valor de las cartas no en grupo

        for (int contador : contadores) {
            if (contador >= 2) {
                totalGrupos++;
            }
        }

        if (totalGrupos > 0) {
            mensaje = new StringBuilder("Los grupos encontrados fueron:\n");
            for (int i = 0; i < contadores.length; i++) {
                if (contadores[i] >= 2) {
                    mensaje.append(Grupo.values()[contadores[i]]).append(" de ").append(NombreCarta.values()[i]).append("\n");
                }
            }
        }

        // Calcular el valor de las cartas no en grupo fuera del bucle
        for (int i = 0; i < TOTAL_CARTAS; i++) {
            if (contadores[cartas[i].getNombre().ordinal()] == 1) {
                valorTotalNoGrupo += obtenerValorCarta(cartas[i].getNombre());
            }
        }

        mensaje.append("Valor total de cartas no en grupo: ").append(valorTotalNoGrupo).append("\n");

        return mensaje.toString();
    }

    public String contarPorPinta() {// Método para contar las cartas que están en pinta
        StringBuilder mensaje = new StringBuilder("Las pintas consecutivas encontradas fueron:\n");

        Pinta[] pintas = Pinta.values();

        int valorTotalNoConsecutivas = 0; // Variable para calcular el valor de las cartas no consecutivas

        for (Pinta pinta : pintas) {
            boolean pintasEncontradas = false;
            int contadorPintasConsecutivas = 1;
            int valorCartasNoConsecutivas = 0; // Variable para acumular el valor de las cartas no consecutivas

            for (int i = 0; i < TOTAL_CARTAS - 1; i++) {
                if (cartas[i].getPinta() == pinta) {
                    if (cartas[i].getIndice() == cartas[i + 1].getIndice()) {//quité acá -1
                        contadorPintasConsecutivas++;
                    } else {
                        contadorPintasConsecutivas = 1;
                    }

                    if (contadorPintasConsecutivas >= 2) {
                        if (!pintasEncontradas) {
                            mensaje.append("");
                            pintasEncontradas = true;
                        }
                        mensaje.append(pinta).append(": ").append(contadorPintasConsecutivas).append(" consecutivas\n");
                    } else {
                        // Acumular el valor de la carta no consecutiva
                        valorCartasNoConsecutivas += obtenerValorCarta(cartas[i].getNombre());
                    }
                }
            }

            // Sumar el valor acumulado de las cartas no consecutivas al valor total
            valorTotalNoConsecutivas += valorCartasNoConsecutivas;
        }

        mensaje.append("Valor total de cartas no consecutivas: ").append(valorTotalNoConsecutivas).append("\n");

        return mensaje.toString();
    }

    public void agregarCartaAGrupoNombre(int indice) {
        if (indice >= 0 && indice < TOTAL_CARTAS) {
            enGrupoNombre[indice] = true;
        }
    }

    public void agregarCartaAGrupoEscalera(int indice) {
        if (indice >= 0 && indice < TOTAL_CARTAS) {
            enGrupoEscalera[indice] = true;
        }
    }

    public int calcularPuntaje() { // Método para cálcular el puntaje de ambos jugadores
        int puntaje = 0;
        for (int i = 0; i < TOTAL_CARTAS; i++) {
            if (!enGrupoNombre[i] && !enGrupoEscalera[i]) {
                NombreCarta nombre = cartas[i].getNombre();
                int valorCarta = obtenerValorCarta(nombre);
                puntaje = valorCarta;

            }
        }

        return puntaje;
    }

    private int obtenerValorCarta(NombreCarta nombre) { // Asignación de puntos a cada una de las cartas
        switch (nombre) {
            case AS:
                return 10;
            case DOS:
                return 2;
            case TRES:
                return 3;
            case CUATRO:
                return 4;
            case CINCO:
                return 5;
            case SEIS:
                return 6;
            case SIETE:
                return 7;
            case OCHO:
                return 8;
            case NUEVE:
                return 9;
            case DIEZ:
            case JACK:
            case QUEEN:
            case KING:
                return 10;
            default:
                return 0;
        }
    }

    public void ordenar() {
        for (int i = 0; i < TOTAL_CARTAS - 1; i++) {
            for (int j = i + 1; j < TOTAL_CARTAS; j++) {
                if (cartas[i].getIndice() > cartas[j].getIndice()) {
                    Carta ct = cartas[i];
                    cartas[i] = cartas[j];
                    cartas[j] = ct;
                }
            }
        }
    }
}
