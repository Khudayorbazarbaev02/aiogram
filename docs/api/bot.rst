using System;
using System.Threading.Tasks;
using Telegram.Bot;
using Telegram.Bot.Args;
using System.Net.Http;

class Program
{
    private static TelegramBotClient botClient;
    private static string apiUrl = "https://api.coingecko.com/api/v3/coins/bitcoin";

    static async Task Main(string[] args)
    {
        botClient = new TelegramBotClient("TOKEN"); // TOKEN o'rniga o'z botingizning tokenini qo'yishingiz kerak
        var me = await botClient.GetMeAsync();
        Console.Title = me.Username;

        botClient.OnMessage += Bot_OnMessage;
        botClient.StartReceiving();

        Console.WriteLine($"Bot ishga tushdi: {me.FirstName}. Botni bekor qilish uchun [Enter] tugmasini bosing.");
        Console.ReadLine();

        botClient.StopReceiving();
    }

    private static async void Bot_OnMessage(object sender, MessageEventArgs e)
    {
        var message = e.Message;

        if (message.Text != null)
        {
            string responseBody = await GetCryptoData();
            await botClient.SendTextMessageAsync(message.Chat.Id, responseBody);
        }
    }

    private static async Task<string> GetCryptoData()
    {
        using (HttpClient client = new HttpClient())
        {
            try
            {
                HttpResponseMessage response = await client.GetAsync(apiUrl);
                response.EnsureSuccessStatusCode();
                return await response.Content.ReadAsStringAsync();
            }
            catch (HttpRequestException e)
            {
                return $"HTTP hatoligi sodir bo'ldi: {e.Message}";
            }
        }
    }
}
