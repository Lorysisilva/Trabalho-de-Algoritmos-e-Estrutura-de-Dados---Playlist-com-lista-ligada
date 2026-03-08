#include <gtk/gtk.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// estrutura da lista ligada
typedef struct Musica {
    char nome[50];
    struct Musica *prox;
} Musica;

Musica *inicio = NULL;

// campo de texto da interface
GtkWidget *entrada;

// função para adicionar música na lista
void adicionar_musica(GtkWidget *widget, gpointer data) {

    const char *nome = gtk_entry_get_text(GTK_ENTRY(entrada));

    if(strlen(nome) == 0)
        return;

    Musica *nova = malloc(sizeof(Musica));
    strcpy(nova->nome, nome);
    nova->prox = NULL;

    if(inicio == NULL){
        inicio = nova;
    } else {

        Musica *temp = inicio;

        while(temp->prox != NULL)
            temp = temp->prox;

        temp->prox = nova;
    }

    gtk_entry_set_text(GTK_ENTRY(entrada), "");
}

// função para mostrar playlist
void mostrar_playlist(GtkWidget *widget, gpointer data) {

    Musica *temp = inicio;

    GString *texto = g_string_new("Playlist:\n");

    while(temp != NULL){
        g_string_append_printf(texto, "%s\n", temp->nome);
        temp = temp->prox;
    }

    GtkWidget *dialogo = gtk_message_dialog_new(
        NULL,
        GTK_DIALOG_MODAL,
        GTK_MESSAGE_INFO,
        GTK_BUTTONS_OK,
        "%s",
        texto->str
    );

    gtk_dialog_run(GTK_DIALOG(dialogo);
    gtk_widget_destroy(dialogo);

    g_string_free(texto, TRUE);
}

int main(int argc, char *argv[]) {

    gtk_init(&argc, &argv);

    // cria janela
    GtkWidget *janela = gtk_window_new(GTK_WINDOW_TOPLEVEL);
    gtk_window_set_title(GTK_WINDOW(janela), "Playlist");
    gtk_window_set_default_size(GTK_WINDOW(janela), 300, 200);

    g_signal_connect(janela, "destroy", G_CALLBACK(gtk_main_quit), NULL);

    // caixa vertical
    GtkWidget *box = gtk_box_new(GTK_ORIENTATION_VERTICAL, 5);
    gtk_container_add(GTK_CONTAINER(janela), box);

    // campo para digitar música
    entrada = gtk_entry_new();
    gtk_box_pack_start(GTK_BOX(box), entrada, FALSE, FALSE, 5);

    // botão adicionar
    GtkWidget *botao_add = gtk_button_new_with_label("Adicionar Musica");
    g_signal_connect(botao_add, "clicked", G_CALLBACK(adicionar_musica), NULL);
    gtk_box_pack_start(GTK_BOX(box), botao_add, FALSE, FALSE, 5);

    // botão mostrar playlist
    GtkWidget *botao_mostrar = gtk_button_new_with_label("Mostrar Playlist");
    g_signal_connect(botao_mostrar, "clicked", G_CALLBACK(mostrar_playlist), NULL);
    gtk_box_pack_start(GTK_BOX(box), botao_mostrar, FALSE, FALSE, 5);

    gtk_widget_show_all(janela);

    gtk_main();

    return 0;
}
