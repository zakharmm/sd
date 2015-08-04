require 'daemons'
require 'telegrammer'
require_relative 'aparat'

class AparatMe
  attr_accessor :bot, :aparat
  def initialize
    p 'INIT'
    @bot = Telegrammer::Bot.new('XXX')
    @aparat = Aparat.new
  end

  def process_message_and_send
    @bot.get_updates do |message|
      puts "In chat #{message.chat.id}, @#{message.from.username} said: #{message.text}"
      rec_message = message.text
      if (!rec_message) || (rec_message.strip! == '')
        p 'ERROR'
      else
        begin      
          if rec_message[0..5] == '/start'
            @bot.send_message(chat_id: message.chat.id, text: "Please send a keyword with /video for example: /video dog")
          elsif rec_message[0..6] == '/video '
            result = @aparat.video_by_search(rec_message[7..-1])
            @bot.send_message(chat_id: message.chat.id, text: "First item of search: #{result}")
          else
            # @bot.send_message(chat_id: message.chat.id, text: "Please send your keyword with /video command.")
          end
        rescue Exception => e
          p e.message
        end
      end
    end
  end
end

Process.daemon(true)
aparat_me = AparatMe.new
aparat_me.process_message_and_send
